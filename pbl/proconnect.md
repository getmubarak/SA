## Scenario: Global Professional Social Network - "ProConnect"

### Business Context
You're the chief architect for "ProConnect," a professional networking platform similar to LinkedIn. The platform serves **900 million members** across 200+ countries and territories, facilitating:
- **Professional profiles** (resume, skills, endorsements, recommendations)
- **Connection network** (1st, 2nd, 3rd degree connections)
- **News feed** (posts, articles, updates from network)
- **Job postings and applications** (20 million active jobs)
- **Messaging** (professional conversations)
- **Company pages** (50 million companies)
- **Content publishing** (articles, newsletters, videos)
- **Skills assessment and learning** (online courses, certifications)
- **Recruiter tools** (InMail, talent search, applicant tracking)
- **Advertising platform** (sponsored content, job ads)
- **Analytics and insights** (profile views, post engagement, company analytics)

The platform processes:
- **5 billion page views per day**
- **100 million searches per day**
- **6 million job applications per day**
- **2 billion connections** between members
- **50 TB of new data per day**

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Web application (primary interface)
- Mobile apps (iOS, Android)
- Recruiter desktop application
- Sales Navigator (premium tool)
- Learning platform
- Third-party integrations (ATS systems, CRMs)

#### 2. Backend Services
- **Profile Service**: User profiles, education, experience, skills
- **Graph Service**: Connection network (1st, 2nd, 3rd degree)
- **Feed Service**: News feed generation and ranking
- **Search Service**: People, jobs, companies, content search
- **Jobs Service**: Job postings, applications, recommendations
- **Messaging Service**: Professional messaging (InMail)
- **Content Service**: Posts, articles, videos
- **Company Service**: Company pages, follower management
- **Notification Service**: Alerts, emails, push notifications
- **Recommendation Engine**: "People You May Know", job recommendations
- **Skills Service**: Skills graph, endorsements, assessments
- **Analytics Service**: Profile views, engagement metrics
- **Advertising Service**: Ad targeting, delivery, billing
- **Learning Service**: Courses, videos, assessments
- **Identity Service**: Authentication, authorization, SSO

#### 3. Infrastructure
- **Multi-region deployment** (US-East, US-West, Europe, Asia-Pacific)
- **Oracle/MySQL clusters** for transactional data
- **Graph database (Neo4j/custom)** for social graph
- **Espresso (LinkedIn's NoSQL)** for profile data
- **Kafka** for event streaming (2 trillion events/day)
- **Hadoop/Spark** for batch processing and analytics
- **Voldemort (distributed key-value)** for session data
- **Elasticsearch** for search
- **Redis/Memcached** for caching
- **HDFS** for data lake (100+ PB)

#### 4. Current Flows

**Feed Generation Flow:**
```
1. User opens feed
2. Feed Service queries Graph Service for connections (1st degree: 500-3000 people)
3. For each connection, fetch recent posts (last 7 days)
4. Query content from Content Service (3,000 connections × 10 posts = 30,000 posts)
5. Apply ranking algorithm (engagement score, recency, relevance)
6. Filter by user preferences and privacy settings
7. Mix in promoted content (ads, recommended posts)
8. Personalize based on user behavior history
9. Return ranked feed (infinite scroll)

Challenge: Generating personalized feed for 900M users
```

**People Search Flow:**
```
1. User searches "Software Engineer at Google in San Francisco"
2. Search Service parses query (job title, company, location)
3. Elasticsearch query across 900M profiles
4. Apply filters: 2nd degree connections, location radius, current company
5. Rank by: relevance, connection strength, profile completeness
6. Faceted search (filter by industry, company, school)
7. Return paginated results (25 per page)

Current issue: Search takes 3-8 seconds, poor relevance
```

**Job Recommendation Flow:**
```
1. User opens "Jobs" tab
2. Jobs Service fetches user profile (skills, experience, preferences)
3. Queries 20 million active jobs
4. ML model ranks jobs by fit:
   - Skills match
   - Experience level
   - Location preference
   - Salary range
   - Company size/industry
   - Connection to current employees (referral potential)
5. Apply diversity (different companies, roles, locations)
6. Return top 50 recommended jobs

Challenge: Real-time ML inference at scale, cold start problem
```

**"People You May Know" (PYMK) Flow:**
```
1. User views profile page
2. Recommendation Engine analyzes:
   - Shared connections (2nd degree)
   - Shared employers (current/past)
   - Shared schools
   - Similar skills/industry
   - Profile view history (stalking signal)
   - Geographic proximity
3. Graph database queries (expensive, multi-hop)
4. Score and rank candidates
5. Filter out already connected, ignored, or blocked
6. Return top 10 suggestions

Challenge: Graph queries are slow (5-10 seconds), real-time calculation impossible
```

**Profile View Notification Flow:**
```
1. User A views User B's profile
2. Analytics Service records view event
3. Publishes to Kafka topic "profile-views"
4. Notification Service consumes event
5. Checks User B's notification preferences
6. Decides whether to notify (premium feature for some)
7. Sends notification (push, email, or in-app)
8. Increments "Who's Viewed Your Profile" count

Challenge: Privacy modes (anonymous viewing for premium), notification spam
```

### The Problem

The platform is experiencing critical issues affecting growth and revenue:

#### Issue 1: Feed Generation Performance Collapse
- **Feed generation takes 5-15 seconds** for active users with many connections
- User with 3,000 connections → 30,000 potential posts to rank
- Feed Service queries 15+ downstream services synchronously
- Each service has 200-500ms latency
- Combined latency: 15 services × 300ms = 4.5 seconds (best case)
- Ranking algorithm is CPU intensive (machine learning inference)
- Feed personalization requires user's interaction history (200MB+ data per user)
- During peak hours (9 AM - 11 AM), feed service crashes
- Database queries for post content cause thundering herd
- Cached feeds go stale (show old content)
- Real-time posts take 10-15 minutes to appear in followers' feeds

#### Issue 2: Graph Database Scalability Crisis
- **Social graph has 2 billion connections** (edges between members)
- Graph queries for "2nd degree connections" scan millions of nodes
- "People You May Know" query takes 8-15 seconds
- During bulk connection imports (data partnerships), graph DB locks up
- Write operations block read operations
- Connection suggestions are stale (regenerated daily in batch)
- 3rd degree connection calculations timeout (>30 seconds)
- Graph database sharding is complex (maintain locality)
- Traversal queries don't parallelize well
- Memory exhausted (graph doesn't fit in RAM - 500GB+ per shard)

#### Issue 3: Search Service Degradation
- **People search** returns irrelevant results
- Search for "Product Manager" returns people with "Manager" in any context
- Faceted navigation (filter by location, company, school) is slow
- Each filter triggers full re-query (not cached)
- Boolean search not working ("Product Manager" AND "AI" should require both)
- Elasticsearch cluster at 90% CPU constantly
- Index size: 2TB and growing 10GB/day
- Index refresh takes 5-10 minutes (new profiles not searchable)
- Typo tolerance poor ("Produktmanager" doesn't find "Product Manager")
- Premium search features (Boolean, advanced filters) available to 10M users
- Regular users get basic search (poor results)

#### Issue 4: Job Search and Application Bottleneck
- **Job search extremely slow** (10-20 seconds)
- 20 million active jobs indexed
- Personalized ranking requires ML inference (2-3 seconds per user)
- "Easy Apply" feature causes application surge (10,000 apps for popular jobs)
- Application data stored in single PostgreSQL database
- Database write throughput maxed out (50,000 writes/second)
- Job application confirmation emails delayed by hours
- Recruiters' applicant dashboards timeout (scanning millions of applications)
- Duplicate applications common (user clicks "Apply" multiple times)
- No deduplication mechanism

#### Issue 5: "People You May Know" Staleness
- **PYMK recommendations updated once daily** (batch job)
- Batch job takes 18-20 hours to complete
- Processes 900M members × average 500 recommendations = 450B calculations
- Graph traversal for each member (expensive)
- Real-time events (new connection, profile update) not reflected until next day
- User connects with person → Still shown in PYMK for 24 hours
- New members have no recommendations (cold start problem)
- PYMK accuracy declining (engagement down 40% year-over-year)
- Batch job failures require full restart (lost progress)

#### Issue 6: Messaging Service Overload
- **InMail and messaging** infrastructure overwhelmed
- Premium feature: InMail allows messaging outside network
- Free users limited to 1st degree connections
- Message delivery delayed during peak (30-60 seconds)
- Read receipts not working (stale, 5-10 minute delay)
- Attachment uploads fail 20% of the time (>5MB files)
- Message threads not loading (timeout after 10 seconds)
- Notifications for messages delayed or missing
- "Typing indicator" doesn't work
- Message search is extremely slow (30+ seconds)
- No real-time synchronization (web vs mobile show different messages)

#### Issue 7: Content Virality and Hot Spots
- **Viral posts cause cascading failures**
- Popular post with 100K likes → 100K write operations
- Like counter updates cause database locks
- Comments on viral posts (10K+ comments) don't load
- Comment pagination broken (shows first 100, rest timeout)
- Share functionality creates duplicate content entries
- Video posts consume massive bandwidth (no CDN optimization)
- High-engagement posts slow down entire feed service
- Engagement metrics (likes, comments, shares) inconsistent across users
- Analytics dashboards show stale counts (hours old)

#### Issue 8: Profile Data Consistency Issues
- **Profile updates propagate slowly** across services
- User updates job title → Takes 10-15 minutes to reflect in search
- Profile cache invalidation broken
- Some services show old data, others show new (split brain)
- Profile completeness score calculation is async (1-hour delay)
- Skills endorsements not reflected in search ranking
- Profile photo updates require manual cache purge
- "Last updated" timestamp incorrect
- Resume upload processing takes 5-10 minutes (PDF parsing)

#### Issue 9: Notification Storm and Spam
- **Notification overload** driving user disengagement
- Average user receives 50-100 notifications per day
- Email notifications batched poorly (10 separate emails for 10 likes)
- Push notifications drain battery (excessive frequency)
- Notification preferences not respected (still receiving after opt-out)
- "Someone viewed your profile" notifications overwhelming (20+ per day for recruiters)
- Group notifications (multiple people liked your post) not implemented
- Notification settings complex (15+ toggles, users confused)
- Spam detection not working (connection requests from fake profiles)

#### Issue 10: Skills Graph and Endorsements Chaos
- **Skills data is inconsistent** and noisy
- Users self-report skills (typos, synonyms, misspellings)
- "JavaScript" vs "Javascript" vs "JS" vs "Java Script" (same skill, 4 entries)
- Skills graph has 50,000+ skill nodes
- No canonical skill taxonomy
- Skills endorsements are gamified and meaningless
- "Endorsement spam" (people endorse without knowledge)
- Skills assessment results not integrated with profile
- Recruiters don't trust skills data (rely on manual screening)
- Skills-based search returns poor results (synonym problem)

#### Issue 11: Company Page Performance Issues
- **Company pages slow to load** (8-15 seconds)
- Large companies (100K+ employees) cause timeout
- Employee list query scans entire member database
- "People who work here" section doesn't scale
- Company follower count updates delayed (batch, daily)
- Company analytics dashboard unusable (30+ second queries)
- Job postings for company not loading (pagination broken)
- Company news feed algorithm poor (shows old posts)
- Admin tools for company page slow and buggy

#### Issue 12: Analytics and Reporting Delays
- **"Who viewed your profile"** data delayed by 6-12 hours
- Post engagement analytics lag by 2-3 hours
- Real-time analytics not actually real-time
- Analytics queries scan petabytes of data (expensive)
- Dashboard loading takes 30-60 seconds
- Export functionality times out (too much data)
- Historical data queries (6+ months) fail
- No sampling or approximation (always exact counts)
- Analytics reports generated in batch overnight (12-hour cycle)
- Recruiters need real-time data (can't wait 12 hours)

#### Issue 13: Advertising Platform Bottleneck
- **Ad targeting system slow** (5-10 seconds to serve ad)
- Targeting criteria complex (location, job title, company, skills, interests)
- Each ad request requires ML inference (audience match)
- Ad inventory management is chaotic (overselling, underselling)
- Sponsored content mixed poorly with organic feed
- Click-through rate (CTR) declining (ad fatigue)
- Ad fraud detection insufficient (bots clicking ads)
- Advertiser dashboard slow (reporting lag)
- Budget pacing incorrect (spend entire budget in first hour)
- No real-time bidding (RTB) integration

#### Issue 14: Learning Platform Integration Issues
- **Learning courses not integrated** with profile
- Course completions don't update profile automatically
- Skills from courses not added to skills section
- Certificates stored separately (users must manually add)
- Course recommendations not personalized
- Progress tracking syncing issues (web vs mobile)
- Video streaming buffering frequently
- Assessment results not reflected in profile skills
- Learning path recommendations poor (not based on career goals)

#### Issue 15: Data Privacy and Compliance Nightmare
- **GDPR compliance difficult** (data scattered across 100+ services)
- User data export takes 30+ days (manual process)
- Right to deletion (RTBF) takes weeks to propagate
- Data retention policies inconsistent across services
- Personal data stored in analytics logs (not encrypted)
- Consent management fragmented (users confused)
- Data breach response slow (can't identify affected users quickly)
- Cross-border data transfer issues (data residency requirements)
- No unified data catalog (don't know where all user data lives)

#### Issue 16: Mobile App Performance Issues
- **Mobile app slow and battery-draining**
- App size: 300MB+ (too large for emerging markets)
- Cold start time: 8-10 seconds
- Feed scrolling janky (UI thread blocked)
- Background sync wakes app every 5 minutes (battery drain)
- Image loading slow (not optimized for mobile)
- Offline mode not working (app requires constant connectivity)
- Crash rate: 2% (unacceptable)
- Mobile search worse than web (limited functionality)
- Push notifications delayed by 5-10 minutes

#### Issue 17: Recruiter Tools Scalability Issues
- **LinkedIn Recruiter product** (premium tool for talent acquisition) struggling
- Talent search queries timeout (searching across 900M profiles)
- Advanced boolean search not working for complex queries
- Saved searches not executing (too expensive)
- InMail sending at scale (1000+ messages) queued for hours
- Candidate pipeline management slow (drag-and-drop times out)
- Applicant tracking system (ATS) integrations breaking
- Recruiter seat licenses expensive (pricing pressure)
- Competing products (Indeed, Glassdoor) have better recruiter tools

#### Issue 18: Rate Limiting and API Abuse
- **Partner APIs being abused** (data scraping, spam)
- No effective rate limiting (easily circumvented)
- Bots creating fake profiles at scale (100K+ per day)
- Connection request spam (mass connection tools)
- Content scraping (external sites copying LinkedIn data)
- API quota enforcement inconsistent
- Webhooks for integrations unreliable (events lost)
- OAuth tokens not expiring properly
- Partner data quality issues (incorrect profile updates)

