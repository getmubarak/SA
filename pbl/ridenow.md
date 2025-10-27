## Scenario: Real-Time Ride-Hailing Platform - "RideNow"

### Business Context
You're the principal architect for "RideNow," a ride-hailing platform operating in 50 cities across multiple countries. The platform connects riders with drivers in real-time, handles 2 million rides per day, has 500,000 active drivers, and 3 million active riders. The company is facing critical technical challenges as they expand to new markets and introduce new features (food delivery, package delivery, ride-sharing pools).

---

## Detailed Functionality

### Current System Architecture

#### 1. Mobile Applications
- Rider App (iOS/Android)
- Driver App (iOS/Android)
- Both apps communicate with backend via REST APIs
- Apps send GPS location updates every 4 seconds
- Maps powered by third-party provider (Google Maps API)

#### 2. Backend Services
- **Matching Service**: Matches riders with nearby drivers
- **Trip Service**: Manages trip lifecycle (request → pickup → dropoff → completion)
- **Location Service**: Tracks real-time driver locations
- **Pricing Service**: Calculates fare estimates and surge pricing
- **Payment Service**: Handles payments, refunds, and driver payouts
- **Notification Service**: Sends push notifications to riders and drivers
- **Rating Service**: Manages rider and driver ratings
- **Fraud Detection Service**: Identifies fraudulent activities
- **Analytics Service**: Business intelligence and reporting

#### 3. Infrastructure
- All services deployed in 3 regional datacenters (US, Europe, Asia)
- **PostgreSQL cluster** for transactional data (trips, users, payments)
- **MongoDB** for driver locations and historical trip data
- **Redis** for session management and temporary data
- **Kafka** for event streaming (partially implemented)
- **REST APIs** for all service communication

#### 4. Current Flows

**Ride Request Flow:**
```
1. Rider opens app and requests ride
2. Rider App sends request to Trip Service with pickup/dropoff locations
3. Trip Service calls Pricing Service for fare estimate
4. Pricing Service queries historical trip data (database)
5. Pricing Service calculates surge multiplier based on demand
6. Trip Service creates trip record in database (status: SEARCHING)
7. Trip Service calls Matching Service
8. Matching Service queries Location Service for nearby drivers
9. Location Service queries MongoDB for drivers within 2km radius
10. Matching Service filters available drivers (status, rating, type)
11. Matching Service sends notifications to top 5 drivers (synchronously)
12. First driver to accept gets the trip
13. Trip Service updates trip record (status: ACCEPTED)
14. Both apps are notified of the match
```

**Location Tracking Flow:**
```
1. Driver App sends GPS coordinates every 4 seconds
2. Location Service receives update
3. Validates and sanitizes coordinates
4. Updates driver location in MongoDB
5. Publishes location update to Kafka topic
6. If driver is on active trip:
   - Updates trip's current location
   - Calculates ETA to destination
   - Sends ETA update to Rider App
```

**Trip Completion Flow:**
```
1. Driver marks trip as complete
2. Trip Service calculates final fare
3. Calls Pricing Service for fare calculation
4. Trip Service calls Payment Service
5. Payment Service charges rider (synchronous)
6. Payment Service updates driver balance
7. Trip Service updates trip status (COMPLETED)
8. Notification Service sends receipt to rider
9. Notification Service prompts for rating
10. Analytics Service records trip data
```

---

## Technical Bottleneck (30-minute analysis challenge)

### The Problem

The platform is experiencing severe issues that are impacting business operations and user satisfaction:

#### Issue 1: Matching Service Breakdown
- During peak hours (Friday/Saturday nights), matching takes 2-5 minutes
- In surge pricing situations, matching fails completely
- 40% of ride requests timeout during peak hours
- Matching Service queries all drivers in a city, then filters in-memory
- For large cities (NYC, London), this means querying 50,000+ driver records
- Database query takes 3-8 seconds during peak
- Notification Service sends messages to drivers sequentially (not in parallel)
- If first 3 drivers reject, process restarts from beginning
- No driver sees multiple ride requests simultaneously

#### Issue 2: Location Data Chaos
- System receives 500,000 GPS updates per second (500k drivers × 1 update/4s)
- Location Service writes every update to MongoDB immediately
- MongoDB write throughput is maxed out (50,000 writes/second)
- Backpressure causes Location Service to crash frequently
- When Location Service is down, drivers appear "offline" to riders
- Historical location data stored forever (5TB and growing)
- Queries for "nearby drivers" scan millions of location records
- Driver locations can be stale by 30-45 seconds during peak

#### Issue 3: Pricing Service Bottleneck
- Every ride request calls Pricing Service 2-3 times:
  - Initial fare estimate
  - Updated estimate if pickup location changes
  - Final fare calculation
- Surge pricing calculation queries last 15 minutes of completed trips
- Queries 100,000+ trip records every time
- During peak hours, Pricing Service response time: 5-12 seconds
- Service becomes bottleneck for entire ride request flow
- Surge multipliers update slowly (10-15 minute lag)
- Riders see outdated pricing information
- Surge pricing zones recalculated for entire city on every request

#### Issue 4: Database Lock Contention
- Trip status updates cause row-level locks in PostgreSQL
- Multiple services try to update same trip simultaneously:
  - Trip Service updates status
  - Location Service updates current location
  - Pricing Service updates fare
  - Payment Service updates payment status
- Deadlocks occur frequently (200-300 per hour during peak)
- Applications retry transactions, amplifying the problem
- Some trips stuck in "limbo" state (neither active nor completed)

#### Issue 5: Payment Processing Failures
- Payment Service makes synchronous calls to payment gateway
- Payment gateway has 2-3 second response time
- During gateway outages (happens 2-3 times/month):
  - Trips cannot be completed
  - Drivers stuck waiting for payment confirmation
  - Riders can't request new trips
- No retry mechanism for failed payments
- Failed payments require manual intervention
- Driver payouts delayed when payments fail

#### Issue 6: Cross-Region Data Inconsistency
- Drivers occasionally work in multiple regions (border cities)
- Driver profile data replicated across regional databases
- No eventual consistency mechanism
- Driver ratings in one region don't reflect in another
- Payment balances get out of sync
- Drivers can be "matched" in two regions simultaneously
- Some trips recorded in multiple regions (double-billing issues)

#### Issue 7: Real-Time Communication Failures
- Push notifications for driver-rider communication are unreliable
- Notification Service uses REST API polling (every 2 seconds)
- During active trips, apps make 900+ API calls per 30-minute trip
- "Driver is arriving" notifications delayed by 30-60 seconds
- Messages between driver and rider have 10-15 second latency
- No way to know if driver/rider has seen messages
- Notifications lost when apps are in background

#### Issue 8: Analytics Service Overload
- All completed trips written to analytics database immediately
- Analytics queries run on same database as operational data
- Dashboard queries cause operational queries to slow down
- Trip data stored in highly normalized structure (12 tables per trip)
- "Real-time" dashboards actually 2-3 hours delayed
- Historical analysis queries timeout (>5 minute execution)
- Peak hour dashboards completely unusable

#### Issue 9: Fraud Detection Lag
- Fraud Detection Service analyzes trips after completion
- Fraudulent trips already paid out to drivers
- Service processes trips in batch (every 30 minutes)
- By the time fraud detected, driver account deleted
- Cannot prevent fraud proactively
- No real-time risk scoring during matching
- Fake GPS locations not detected until post-trip analysis

#### Issue 10: Cascading Failures
- When any service goes down, entire platform affected
- No circuit breakers between services
- Retry storms amplify outages
- Services crash from memory leaks after 48-72 hours
- Manual restarts required
- No graceful degradation - failures are all-or-nothing
- Average downtime: 45 minutes per incident (3-4 incidents/month)

### Requirements

#### Functional Requirements
1. **Match riders with drivers in <5 seconds** even during peak hours
2. **Track driver locations in real-time** with <2 second latency
3. **Calculate accurate surge pricing** within 10-second windows
4. **Support 10 million rides per day** (5x current volume)
5. **Process 2 million GPS updates per second**
6. **Enable real-time bidirectional communication** between drivers and riders
7. **Detect fraud in real-time** before trip completion
8. **Handle payment failures gracefully** without blocking trips
9. **Provide consistent experience** across all regions

#### Non-Functional Requirements
10. **99.99% availability** for core ride-matching flow
11. **Graceful degradation** - non-critical features can fail independently
12. **Sub-second response times** for location queries
13. **Horizontal scalability** for all services
14. **Data consistency** - no double-matching, no double-billing
15. **Cost efficiency** - reduce database load by 70%
16. **Real-time analytics** for business dashboards (<1 minute lag)
17. **Audit trail** for all financial transactions
18. **Multi-region active-active** deployment
