## Scenario: Cloud File Storage & Synchronization Platform - "CloudSync"

### Business Context
You're the lead architect for "CloudSync," a cloud file storage and synchronization service similar to Dropbox/Google Drive. The platform serves 50 million users who store, sync, and share files across multiple devices (desktop, mobile, web). Users expect real-time synchronization, offline access, file versioning, and collaborative features. The platform stores 5 petabytes of data and handles 2 billion file operations per day.

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Desktop sync clients (Windows, macOS, Linux)
- Mobile apps (iOS, Android)
- Web application (React-based)
- All clients sync files in real-time
- Local file system monitoring for changes
- Chunk-based upload/download for large files

#### 2. Backend Services
- **Upload Service**: Handles file uploads from clients
- **Download Service**: Serves file content to clients
- **Sync Service**: Coordinates file synchronization across devices
- **Metadata Service**: Manages file/folder metadata and hierarchy
- **Version Service**: Tracks file versions and enables restoration
- **Sharing Service**: Manages file/folder sharing and permissions
- **Search Service**: Full-text search across files
- **Notification Service**: Real-time sync notifications
- **Thumbnail Service**: Generates previews for images/videos
- **Collaboration Service**: Real-time collaborative editing

#### 3. Infrastructure
- Services deployed across 3 regions (US-East, US-West, Europe)
- **PostgreSQL cluster** for metadata (files, folders, users, permissions)
- **Object Storage (S3)** for file content (5 PB)
- **Redis** for caching metadata and active sessions
- **Kafka** for event streaming
- **Elasticsearch** for file search
- **WebSocket servers** for real-time sync notifications

#### 4. Current Flows

**File Upload Flow:**
```
1. User saves file to CloudSync folder (1GB video file)
2. Desktop client detects file change via file system watcher
3. Client computes file hash (SHA-256)
4. Client checks with Metadata Service if file exists (deduplication check)
5. Client breaks file into 4MB chunks
6. Client uploads chunks sequentially to Upload Service
7. Upload Service writes each chunk to S3
8. After all chunks uploaded, client notifies Metadata Service
9. Metadata Service creates file record in PostgreSQL
10. Metadata Service publishes "file-uploaded" event to Kafka
11. Sync Service consumes event
12. Sync Service queries PostgreSQL for all user's devices
13. Sync Service sends push notification to each device
14. Other devices download the new file
```

**File Synchronization Flow:**
```
1. User edits document.txt on Device A
2. Device A uploads new version
3. Sync Service detects change
4. Sync Service queries for all active devices (REST call to Metadata Service)
5. For each device (Device B, C, D):
   - Check if device is online (query Redis)
   - Send WebSocket notification
   - Device downloads updated file
6. Version Service creates new version entry in database
7. Old version moved to version history
```

**File Sharing Flow:**
```
1. User shares folder with 500 collaborators
2. Client calls Sharing Service with folder ID and user list
3. Sharing Service validates permissions (query PostgreSQL)
4. For each of 500 users:
   - Create permission record in database (500 INSERT statements)
   - Query user's devices
   - Send notification to each device
5. Process takes 30-45 seconds for 500 users
6. User sees "Sharing..." spinner for entire duration
```

**Folder Listing Flow:**
```
1. User opens folder with 10,000 files in web app
2. Client requests folder contents from Metadata Service
3. Metadata Service queries PostgreSQL:
   - Recursive query for folder hierarchy
   - Join with permissions table
   - Join with versions table
   - Fetch thumbnail URLs
4. Query returns 10,000 rows
5. Response payload: 50MB JSON
6. Client renders 10,000 files (browser freezes)
7. Query takes 8-12 seconds
8. User sees loading spinner
```

---
### The Problem

The platform is experiencing severe issues affecting user experience and operational costs:

#### Issue 1: Upload Performance Degradation
- Large file uploads (>1GB) take 20-30 minutes
- Upload process is sequential: chunk 1 → chunk 2 → chunk 3...
- If upload fails at chunk 250/256, entire upload must restart
- No resume capability
- Upload Service becomes bottleneck during peak hours
- Single instance of Upload Service handles all uploads for a region
- During peak hours (9 AM - 5 PM), upload queue backs up 2-3 hours
- Users abandon uploads due to slow speeds
- Mobile uploads drain battery (staying awake for 30+ minutes)

#### Issue 2: Synchronization Storms
- When user uploads file, ALL devices notified simultaneously
- User with 10 devices creates 10 parallel downloads
- Popular shared folders (1,000+ members) trigger 1,000+ simultaneous downloads
- Causes download server overload
- Sync Service sends individual notifications (not batched)
- For shared folder with 1,000 members × 3 devices each = 3,000 notifications
- Network saturation during sync storms
- Some devices never receive notifications (dropped messages)
- Users manually force sync, amplifying the problem

#### Issue 3: Metadata Database Overload
- Every file operation queries PostgreSQL
- File listings require complex recursive queries with multiple joins
- Large folders (10,000+ files) take 10-15 seconds to list
- Database CPU at 85-95% constantly
- Slow queries block fast queries (head-of-line blocking)
- 200-300 deadlocks per hour during peak
- Metadata Service caches aggressively, but cache invalidation is broken
- Users see stale file listings (files deleted but still show up)
- Folder hierarchy queries scan millions of rows

#### Issue 4: Deduplication Inefficiency
- Deduplication check requires querying entire files table
- Query: "SELECT file_id WHERE hash = $1" scans 50 billion file records
- Takes 5-8 seconds per check
- No bloom filter or other optimization
- Same file uploaded by 1,000 users → 1,000 database queries
- Duplicate files still stored in S3 (dedup not working in practice)
- Storage costs 40% higher than necessary
- S3 bill: $2M per month (could be $1.2M with proper dedup)

#### Issue 5: Version Storage Explosion
- Every file edit creates new version
- All versions stored permanently (no expiration)
- User edits document 500 times → 500 versions stored
- Autosave feature creates version every 30 seconds
- 1 hour of editing = 120 versions
- Version storage growing 200TB per month
- Old versions rarely accessed but consuming storage
- No delta compression (each version is complete file)
- Version history queries timeout for files with 1,000+ versions

#### Issue 6: Search Service Breakdown
- Elasticsearch indexes full content of every file
- Index size: 800GB and growing 20GB per day
- Search queries take 10-20 seconds for common terms
- During file uploads, search indexing slows down upload processing
- Search indexing is synchronous (blocking upload completion)
- PDF and Office documents extracted and indexed (CPU intensive)
- Some large files (100MB+) cause indexing to timeout
- Files not searchable until indexing completes (2-3 hour delay)

#### Issue 7: Thumbnail Generation Bottleneck
- Thumbnail Service generates previews synchronously
- When user uploads 1,000 photos, thumbnails generated one-by-one
- Takes 30-60 minutes to generate all thumbnails
- Folder listing waits for thumbnails (users see loading spinners)
- Thumbnail generation for videos is extremely slow (2-3 minutes each)
- Service crashes with out-of-memory when processing 4K videos
- Failed thumbnail generation causes upload to fail
- No retry mechanism

#### Issue 8: Sharing Performance Issues
- Sharing folder with 1,000 users takes 2-3 minutes
- Each share creates database record synchronously
- No batch operations
- Revoking access even slower (must check all files recursively)
- Permission checks on every file operation:
  - Download → check permission
  - Upload → check permission
  - Delete → check permission
- Permission query joins 4 tables
- Shared folders with complex nested permissions take 5+ seconds to validate

#### Issue 9: Conflict Resolution Failures
- When two devices edit same file simultaneously, conflicts occur
- Conflict resolution is "last write wins"
- User's work gets silently overwritten
- No operational transformation or CRDT
- Conflicted copies created automatically ("document (conflicted copy).txt")
- Users have hundreds of conflicted copies
- No UI to resolve conflicts
- Collaborative editing feature doesn't prevent conflicts

#### Issue 10: Cross-Region Sync Delays
- User uploads file in US-East region
- File metadata replicated to other regions (eventual consistency)
- Replication lag: 2-5 minutes
- User in Europe doesn't see file for 5 minutes
- If user tries to upload same file in Europe, duplicate created
- No distributed transaction coordination
- Cross-region conflicts common
- Some files "lost" during region failover

#### Issue 11: Bandwidth Cost Explosion
- All file downloads served from central S3 buckets
- User in Asia downloads from US-East S3
- Slow download speeds (100-200 KB/s)
- High egress costs ($0.09 per GB)
- Bandwidth bill: $800K per month
- Popular files downloaded repeatedly (no edge caching)
- Video streaming is unusable (constant buffering)

#### Issue 12: Real-Time Collaboration Failures
- Collaborative editing uses WebSocket for real-time updates
- Each keystroke sends update to server
- Server broadcasts to all collaborators
- With 50 simultaneous editors, generates 1,000+ messages/second
- WebSocket server overwhelms
- Message delivery not guaranteed (UDP-like behavior)
- Edits arrive out of order
- No conflict resolution for simultaneous edits
- Documents corrupted when many people edit simultaneously
- Users lose work frequently

#### Issue 13: Mobile App Performance
- Mobile app syncs entire file tree on startup
- Downloads metadata for all 100,000 files user has
- Takes 30-60 seconds to open app
- Drains battery
- Consumes cellular data
- Selective sync not implemented
- Background sync wakes app every 5 minutes
- iOS kills app for excessive background activity
- Users disable sync to save battery

#### Issue 14: Disaster Recovery Gaps
- Daily backups of PostgreSQL database
- S3 files are versioned but no point-in-time recovery
- If database corrupted, lose up to 24 hours of metadata
- Cannot recover deleted files after 30 days
- No test of disaster recovery procedures
- RTO: 8-12 hours
- RPO: 24 hours
- Regulatory requirements (GDPR, HIPAA) not met for some customers


