## Scenario: Global Media Streaming Platform - "StreamWave"

### Business Context
You're the principal architect for "StreamWave," a video streaming platform similar to Netflix/YouTube. The platform has 5 million active users across North America, Europe, and Asia. Users can upload videos, watch content, comment, and receive personalized recommendations. The company is experiencing growth issues and preparing for a major marketing campaign expected to triple the user base.

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Web application (React)
- Mobile apps (iOS/Android)
- Smart TV applications
- All clients directly access backend APIs

#### 2. Backend Services
- **Video Service**: Handles video uploads, processing, and metadata
- **Streaming Service**: Serves video content to users
- **User Service**: Authentication, profiles, watch history
- **Recommendation Service**: ML-based content recommendations
- **Comment Service**: Manages user comments and replies
- **Analytics Service**: Tracks viewing metrics and user behavior
- **Search Service**: Full-text search for videos

#### 3. Infrastructure
- All services hosted in **US-East datacenter only**
- Video files stored in **centralized object storage** (S3-like)
- **MongoDB cluster** for metadata (videos, users, comments)
- **PostgreSQL** for analytics data
- **Elasticsearch** for search indexing
- **Redis** for session management

#### 4. Current Flows

**Video Upload Flow:**
```
User uploads video (up to 4GB)
→ Video Service receives entire file
→ Saves to object storage
→ Triggers transcoding job (CPU-intensive)
→ Generates multiple qualities (1080p, 720p, 480p, 360p)
→ Updates database with video metadata
→ Indexes in Elasticsearch
→ Returns success to user
```

**Video Playback Flow:**
```
User requests video
→ Streaming Service authenticates user
→ Checks user subscription status (database query)
→ Fetches video metadata (database query)
→ Records analytics event (database write)
→ Generates signed URL for video file
→ Returns video stream to client
```

**Recommendation Flow:**
```
User opens home page
→ Recommendation Service queries user's watch history (database)
→ Queries user preferences (database)
→ Runs ML model (takes 2-4 seconds per user)
→ Fetches metadata for recommended videos (database)
→ Returns personalized recommendations
```

---

## Technical Bottleneck (30-minute analysis challenge)

### The Problem

The platform is experiencing critical issues affecting user experience and operational costs:

#### Issue 1: Global Latency Problems
- Users in Europe experience 800ms-1200ms API response times
- Users in Asia experience 1500ms-2000ms response times
- Video buffering is frequent for international users
- 34% of international users abandon videos due to loading times
- All traffic routes through US-East datacenter

#### Issue 2: Video Upload Failures
- Large video uploads (>1GB) fail 45% of the time
- When uploads fail, users must restart from beginning
- Upload process locks the Video Service thread for entire duration (20-30 minutes for large files)
- During peak hours, Video Service becomes unresponsive
- Transcoding jobs queue up, some videos take 6 hours to process

#### Issue 3: Recommendation Service Meltdown
- Generating recommendations takes 2-4 seconds per user
- ML model runs on every single request (no caching)
- During peak hours (8pm-11pm), service times out
- Home page load times exceed 10 seconds
- Recommendation Service consumes 80% of total infrastructure cost
- Same recommendations are generated repeatedly for the same user

#### Issue 4: Database Hotspots
- Popular videos (viral content) cause database read spikes
- Single video metadata query executed millions of times per hour
- Watch history table grows unbounded (300GB+)
- Analytics writes slow down video playback requests
- Database becomes bottleneck during viral events

#### Issue 5: Search Service Degradation
- Elasticsearch cluster struggles with indexing new videos
- Search queries slow down during high upload periods
- Index size is 500GB and growing 10GB per day
- Full-text search across all fields causes high CPU usage
- Historical content (>1 year old) rarely searched but fully indexed

#### Issue 6: Unnecessary API Calls
- Mobile apps fetch user profile on every screen change
- Video metadata refreshed every 10 seconds during playback
- Comments section polls server every 5 seconds for updates
- Clients make 50-80 API calls per user session
- 70% of API traffic is redundant data fetches

#### Issue 7: Analytics Overload
- Every video view writes to analytics database immediately
- Analytics service can't keep up with write volume (50,000 events/second)
- Backpressure causes video playback API to slow down
- Analytics queries (for creator dashboards) take 30+ seconds
- Real-time analytics not actually "real-time" (6-8 hour delay)

#### Issue 8: Content Delivery Inefficiency
- All video files served from single region storage
- Bandwidth costs are $400,000/month and growing
- Popular videos fetched from origin storage repeatedly
- 5% of videos account for 80% of bandwidth usage
- Storage API rate limits hit during viral events
