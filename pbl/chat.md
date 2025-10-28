## Scenario: Global Real-Time Messaging Platform - "ChatConnect"

### Business Context
You're the principal architect for "ChatConnect," a global instant messaging platform similar to WhatsApp/Telegram. The platform serves **2 billion users** across 180+ countries, handling:
- **100 billion messages per day** (1.2 million messages per second average)
- **Peak load**: 4 million messages per second during New Year celebrations
- **End-to-end encrypted messaging** (privacy-first design)
- **Group chats** (up to 256 members)
- **Media sharing** (photos, videos, documents, voice messages)
- **Voice and video calls** (1-on-1 and group)
- **Status updates** (24-hour ephemeral content)
- **Multi-device support** (phone, web, desktop - up to 4 linked devices)
- **Offline message delivery** (messages queued until recipient online)

The platform must maintain **99.999% availability** with **sub-second message delivery** globally while using minimal server resources (WhatsApp famously ran on ~50 engineers and minimal infrastructure).

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Mobile apps (iOS, Android) - primary interface
- Web application (browser-based)
- Desktop applications (Windows, macOS, Linux)
- All clients maintain persistent connections for real-time messaging
- End-to-end encryption implemented client-side (Signal Protocol)

#### 2. Backend Services
- **Connection Service**: Maintains persistent WebSocket connections with clients
- **Message Router**: Routes messages between users
- **Group Service**: Manages group membership and message distribution
- **Media Service**: Handles photo/video/document uploads and downloads
- **Call Service**: Manages voice/video call signaling
- **Status Service**: Handles 24-hour ephemeral status updates
- **User Service**: User profiles, contact lists, privacy settings
- **Notification Service**: Push notifications for offline users
- **Device Management Service**: Manages linked devices (multi-device)
- **Presence Service**: Tracks user online/offline status and "last seen"
- **Backup Service**: Encrypted chat backups to cloud storage

#### 3. Infrastructure
- **Global deployment** across 15+ regions (Asia, Europe, Americas, Africa, Middle East)
- **PostgreSQL clusters** for user data, contact lists, group metadata
- **Cassandra clusters** for message metadata and routing information
- **Redis clusters** for online user presence and message queuing
- **Object storage (S3)** for media files (photos, videos, documents)
- **TURN/STUN servers** for WebRTC voice/video calls
- **Certificate pinning** and end-to-end encryption infrastructure

#### 4. Current Flows

**One-on-One Message Flow:**
```
1. User A (India) sends message to User B (USA)
2. Message encrypted on User A's device (Signal Protocol)
3. Client establishes WebSocket connection to nearest server (Mumbai)
4. Message Router receives encrypted message
5. Looks up User B's current connection (if online)
6. Routes encrypted message through global network to USA server
7. USA server delivers to User B's device
8. User B's device decrypts message locally
9. Delivery receipt sent back to User A
10. If User B offline, message queued in Redis/database
11. When User B comes online, queued messages delivered

Total latency target: <200ms globally
```

**Group Message Flow:**
```
1. User A sends message to group with 50 members
2. Message encrypted once on User A's device
3. Message Router receives message
4. Looks up all 50 group members from Group Service
5. Creates 50 individual encrypted copies (sender keys)
6. Routes each copy to recipient's connection server
7. For offline users (30/50), messages queued
8. For online users (20/50), delivered immediately
9. As offline users come online, queued messages delivered
10. Read receipts collected from all members

Challenge: How to efficiently fanout to large groups?
```

**Media Upload/Download Flow:**
```
1. User A shares 50MB video with User B
2. Video encrypted on User A's device
3. Upload to nearest Media Service (chunked upload)
4. Media Service stores in object storage (S3)
5. Generates media ID and thumbnail
6. Message Router sends media notification to User B
7. User B's app downloads thumbnail first (instant preview)
8. User B clicks to view → downloads full video
9. Video decrypted on User B's device
10. Media files retained for 30 days, then deleted

Current issue: Large media uploads slow, unreliable
```

**Voice/Video Call Flow:**
```
1. User A initiates call to User B
2. Call Service sends call invitation via Message Router
3. If User B accepts, WebRTC negotiation begins
4. STUN server helps discover public IPs
5. If direct P2P possible, media flows peer-to-peer
6. If blocked by NAT/firewall, TURN server relays media
7. Call signaling remains through Call Service
8. End-to-end encrypted using DTLS-SRTP

Challenge: 40% of calls require TURN relay (expensive)
```

**Multi-Device Synchronization:**
```
1. User has phone, web, and desktop connected
2. Message arrives at phone (primary device)
3. Message must sync to web and desktop simultaneously
4. All devices maintain independent WebSocket connections
5. Message Router fanout to all connected devices
6. Encryption keys synced across devices
7. "Last seen" and "typing indicator" synced in real-time

Challenge: Keeping 4 devices in sync consistently
```

---

### The Problem

The platform is experiencing severe issues affecting billions of users:

#### Issue 1: Connection Storm During Peak Events
- During New Year midnight, **500 million users** send messages simultaneously
- Connection Service overwhelmed: 4 million new connections per minute
- WebSocket servers crash due to memory exhaustion (each connection = ~100KB RAM)
- Connection handshake takes 2-3 seconds (normally <100ms)
- Message delivery latency spikes from 200ms to 30+ seconds
- 15-20% of messages lost during peak
- Server CPU at 100% just maintaining connections (no capacity for message routing)
- Load balancers struggle to distribute connections evenly
- Some servers have 5 million connections, others have 50,000 (imbalance)

#### Issue 2: Message Delivery Failures
- **Offline message queue** growing unbounded (2TB+ in Redis)
- Users who are offline for days have 10,000+ queued messages
- When they come online, message flood crashes client app
- Message ordering not guaranteed across regions
- User sends messages 1, 2, 3 → Recipient receives 1, 3, 2 (out of order)
- Duplicate message delivery common (20% of messages delivered twice)
- No idempotency mechanism
- Message loss during server failures (5-10 messages per incident)
- Acknowledgment system unreliable (messages marked "sent" but never delivered)

#### Issue 3: Group Message Fanout Bottleneck
- Large groups (256 members) cause message delays
- Sending message to 256-member group takes 5-8 seconds
- Message Router creates 256 individual routing tasks
- Database queries for each member's connection info (256 queries)
- If even one member has connectivity issue, entire group send slows down
- Group member changes not propagated quickly (cached for 30 minutes)
- User removed from group still receives messages for 30 minutes
- Group messages consume 70% of Message Router capacity
- Cannot support groups larger than 256 (business wants 1,000+)

#### Issue 4: Media Service Overload
- **Media uploads** fail 30% of the time for files >10MB
- Upload process is synchronous and single-threaded
- If upload fails at 95%, must restart from beginning (no resume)
- Media Service stores all media forever (no cleanup)
- Storage costs: $8 million per month and growing 15% monthly
- Popular media (viral videos) downloaded millions of times
- Each download fetches from origin storage (expensive egress)
- No CDN or edge caching implemented
- Thumbnail generation is synchronous (blocks upload)
- Video transcoding takes 5-10 minutes (user waits)

#### Issue 5: Voice/Video Call Quality Issues
- **40% of calls routed through TURN relay** (should be <10%)
- TURN servers are bottleneck (limited capacity, expensive)
- Call quality poor during peak hours (lag, choppy audio)
- Call drops when user switches networks (WiFi → cellular)
- No automatic reconnection
- Call Service maintains state in memory (lost on restart)
- Active call data not replicated (single point of failure)
- Group video calls (4+ participants) have 5-second audio lag
- Screen sharing not supported due to bandwidth constraints

#### Issue 6: Cross-Region Latency
- User in Brazil messaging user in India: 800ms-1200ms latency
- All messages route through primary US datacenter first
- No direct region-to-region routing
- Message Router in each region queries central database
- Database in US-East → Asia queries have 200-300ms latency
- Contact list queries fetch from central database (slow)
- Presence updates (online/offline) propagate slowly across regions
- User goes offline in Asia → Takes 10-15 seconds to show offline in Europe

#### Issue 7: Database Hotspots and Scaling
- **PostgreSQL primary** at 85% CPU constantly
- Write operations bottleneck (all writes go to single primary)
- User profile updates cause table locks
- Contact list queries scan millions of rows
- Group membership queries require 5-table joins
- "Last seen" timestamp updated on every message (billions of writes/day)
- Read replicas lag behind primary by 2-5 seconds
- During lag, users see stale data (messages from 5 seconds ago)
- Database connection pool exhaustion during peaks (5,000 connection limit)
- Deadlocks occur 500-1,000 times per hour

#### Issue 8: Presence Service Breakdown
- Tracking 500 million concurrent online users
- Each user's online status stored in Redis
- Status updates: 50,000 writes per second
- Friends list queries: 100,000 reads per second (each user checks 50+ contacts)
- Redis cluster at capacity (128GB RAM per node, 20 nodes)
- "Last seen" precision is 5 minutes (users want real-time)
- "Typing indicator" doesn't work in groups (too much traffic)
- Presence updates use 30% of total bandwidth
- Cannot distinguish "online on phone" vs "online on web"

#### Issue 9: Status Feature Overload
- **Status updates** (24-hour ephemeral stories) used by 500 million users daily
- Each status view generates download request
- Popular user posts status → 10 million views → 10 million downloads
- Status media stored in object storage (no edge caching)
- Status view count updates create write storm (millions of increments/second)
- Database cannot keep up with view count updates
- View counts are inaccurate (lag by hours)
- Status expiration (24-hour cleanup) is batch job that takes 6 hours
- Expired statuses still visible for hours after expiration

#### Issue 10: Multi-Device Sync Failures
- User's devices go out of sync frequently
- Phone shows 50 unread messages, web shows 0
- Message read on phone, still unread on desktop (15-minute delay)
- Deleted messages reappear on other devices
- No conflict resolution when same message deleted on multiple devices
- Encryption key sync fails 5% of the time (device can't decrypt messages)
- Device linking takes 30-45 seconds (QR code scan)
- Maximum 4 devices supported (users want more)
- Device state stored in primary database (single source of truth bottleneck)

#### Issue 11: Notification Service Reliability
- **Push notifications** delayed by 5-30 seconds
- During message bursts, notification queue backs up to 5 million
- iOS APNs and Android FCM rate limits hit frequently
- Retry logic causes duplicate notifications (user gets 3 notifications for 1 message)
- Notification grouping not working (100 individual notifications instead of "100 new messages")
- High-priority notifications (calls) mixed with low-priority (group messages)
- No priority queue
- When notification fails, no fallback mechanism
- Silent notifications for background sync fail 40% of time (iOS restrictions)

#### Issue 12: End-to-End Encryption Overhead
- **Signal Protocol** requires per-message encryption/decryption
- Group messages require encrypting message 256 times (once per recipient)
- Encryption on low-end Android devices takes 500ms per message
- Key ratcheting (perfect forward secrecy) adds latency
- Encryption key exchange for new contacts takes 2-3 round trips
- "Safety number verification" workflow confusing to users (rarely used)
- Backup encryption keys lost → All message history lost (no recovery)
- Multi-device encryption key sync complex and error-prone

#### Issue 13: Offline Message Accumulation
- User offline for 7 days → 5,000+ queued messages
- When comes online, all 5,000 messages delivered at once (app crashes)
- Message queue stored in Redis (in-memory, expensive at scale)
- Old queued messages (>7 days) never delivered (dropped silently)
- No prioritization (important messages from boss same as group spam)
- Offline user still counted in group (group admin can't tell who's active)
- Message queue per user growing to 500MB+ (large groups, media)

#### Issue 14: Search and Message History
- **Search feature** is extremely slow (10-15 seconds)
- All messages stored locally on device (SQLite database)
- Search scans entire local database (millions of messages)
- No server-side search (end-to-end encryption prevents indexing)
- Users changing phones lose all message history (if no backup)
- Backup to cloud takes 30+ minutes for active users (1GB+ database)
- Restore from backup takes 45+ minutes
- Searching media messages requires downloading all media first

#### Issue 15: Rate Limiting and Anti-Spam
- **Spam and abuse** rampant in groups
- No effective rate limiting (spammers send 1,000 messages/minute)
- Group admins have no tools to moderate (only kick members)
- Mass message broadcasting to 1,000 groups (spam campaigns)
- Bots scraping phone numbers from groups
- No CAPTCHA or verification for new accounts
- Phone number verification via SMS (SMS fraud/spoofing common)
- Users can create unlimited groups (100 groups per phone number)
- No detection of automated behavior

#### Issue 16: Infrastructure Cost Explosion
- Running at scale is extremely expensive:
  - WebSocket connections: 500 million × 100KB = 50TB RAM just for connections
  - Message routing: 1.2 million messages/second × $0.001 = $100,000/day compute
  - TURN relay: 40% of calls × $0.05/minute = $20 million/month
  - Media storage: 500PB × $0.02/GB = $10 million/month
  - Data transfer: 100PB/month × $0.08/GB = $8 million/month
- Total infrastructure: **$50 million/month** and growing 20% annually
- Need to reduce costs by 60% while maintaining quality


