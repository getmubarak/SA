## Scenario: National Railway Reservation & Operations Platform - "RailConnect"

### Business Context
You're the chief architect for "RailConnect," a comprehensive railway management system for Indian Railways - one of the world's largest railway networks. The platform handles:
- **12,000+ trains daily** across 7,000+ stations
- **23 million passengers per day**
- **1.4 billion tickets annually** (120 million per month)
- **Advanced Reservation Period (ARP)**: 120 days before journey
- **Tatkal bookings**: Opens 24 hours before journey (premium, limited quota)
- **Dynamic pricing**, **waitlist management**, **chart preparation**
- **Real-time train tracking**, **platform changes**, **delay notifications**
- **Freight operations**, **crew management**, **maintenance scheduling**
- **Multiple booking channels**: Website, mobile app, IRCTC agents, railway counters, travel agents

The system faces extreme challenges:
- **Peak load**: 2000+ bookings per second during Tatkal window (10 AM sharp)
- **Festival rush**: 10x normal traffic during Diwali, Holi, summer vacations
- **Complex business rules**: quotas (general, ladies, disabled, defense, tatkal), dynamic fares, waitlist clearance
- **Multi-tenancy**: Different user classes (passengers, agents, railway staff, vendors)
- **Legacy integration**: 40-year-old mainframe systems still in use

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Web portal (IRCTC website)
- Mobile apps (iOS, Android)
- Agent terminals (desktop application)
- Railway counter POS systems
- USSD/SMS booking (*139# service)
- Third-party aggregators (MakeMyTrip, Paytm, etc.)

#### 2. Backend Services
- **Booking Service**: Handles ticket reservations
- **Search Service**: Train availability search between stations
- **Seat Allocation Service**: Assigns berths/seats based on preferences
- **Waitlist Management Service**: Manages RAC (Reservation Against Cancellation) and waitlist
- **Chart Preparation Service**: Finalizes passenger list 4 hours before departure
- **Cancellation Service**: Handles refunds and waitlist clearance
- **Pricing Service**: Dynamic fare calculation, surge pricing
- **Payment Service**: Multiple payment gateways (cards, UPI, wallets, net banking)
- **PNR Status Service**: Real-time booking status
- **Train Schedule Service**: Real-time train running status
- **Station Service**: Platform allocation, train arrival/departure
- **Notification Service**: SMS, email, push notifications
- **Refund Service**: Processes cancellation refunds
- **User Service**: Authentication, profiles, booking history
- **Quota Management Service**: Manages seat quotas across categories
- **Route Management Service**: Train routes, stops, distances

#### 3. Infrastructure
- **Primary Datacenter**: CRIS (Centre for Railway Information Systems), New Delhi
- **Disaster Recovery Site**: Secondary datacenter in Mumbai
- **Oracle RAC cluster** for transactional data (bookings, users, payments)
- **Legacy mainframe** for freight operations and crew management
- **MySQL** for train schedules and routes
- **Redis** for session management and rate limiting
- **Elasticsearch** for train search and PNR lookups
- **Kafka** for event streaming (partially implemented)
- **Load balancers** across 500+ application servers

#### 4. Current Flows

**Ticket Booking Flow (Tatkal - Peak Load):**
```
1. 10:00:00 AM - Tatkal quota opens for train departing tomorrow
2. 500,000 users simultaneously click "Book Now"
3. All requests hit Booking Service
4. Booking Service calls Search Service to check availability
5. Search Service queries Oracle database for seat availability
6. Query joins 8 tables: trains, routes, quotas, bookings, waitlist, fare_rules, stations, seat_map
7. For each of 500,000 requests, query takes 2-5 seconds
8. Booking Service calls Seat Allocation Service
9. Seat Allocation Service locks seat row in database (pessimistic lock)
10. Calls Pricing Service for fare calculation (dynamic pricing based on demand)
11. Pricing Service queries last 1 hour's bookings (100,000+ records)
12. Booking Service creates booking record with status "BLOCKED" (15-minute hold)
13. User redirected to Payment Service
14. Payment gateway has 30-second timeout
15. If payment successful, booking confirmed
16. If payment fails or timeout, seat released back to pool
17. Quota updated, waitlist processed
18. Chart preparation triggered if train is full
19. SMS and email sent via Notification Service
```

**Train Search Flow:**
```
1. User searches: Mumbai to Delhi on 15th Jan
2. Search Service queries database for all trains on route
3. Query scans train_schedules table (50 million records)
4. Filters by source, destination, date
5. For each matching train (200+ trains):
   - Check seat availability across all classes (Sleeper, 3AC, 2AC, 1AC)
   - Calculate fare for each class
   - Check quota availability
   - Determine waitlist position if full
6. Returns paginated results
7. Query takes 8-15 seconds during peak hours
8. User sees loading spinner
```

**Waitlist Clearance Flow:**
```
1. Passenger cancels confirmed ticket
2. Cancellation Service updates booking status to "CANCELLED"
3. Triggers refund calculation based on cancellation time
4. Calls Waitlist Management Service
5. Waitlist Service queries all waitlisted passengers for same train/class
6. Sorts by booking time (FIFO)
7. Promotes first waitlisted passenger to RAC
8. Promotes first RAC passenger to confirmed
9. Updates seat allocation
10. Sends notifications to promoted passengers
11. Entire process is synchronous (blocks cancellation)
12. For popular trains with 500+ waitlisted passengers, takes 2-3 minutes
```

**Chart Preparation Flow (4 hours before departure):**
```
1. Scheduled job runs at T-4 hours
2. Chart Preparation Service queries all bookings for train
3. Processes cancellations, no-shows, upgrades
4. Finalizes seat allocations
5. Generates PDF chart for train conductor
6. Updates all booking statuses
7. Sends final confirmation to all passengers
8. For long-distance trains (2000+ passengers), takes 30-45 minutes
9. During this time, new bookings blocked
```

### The Problem

The platform is experiencing catastrophic failures affecting millions of passengers:

#### Issue 1: Tatkal Booking Meltdown (Critical)
- Tatkal quota opens at 10:00:00 AM sharp
- 2000+ booking requests per second for 5-10 minutes
- Database connection pool (2000 connections) exhausted in 3 seconds
- 95% of requests timeout (60-second timeout)
- Users see "Service Unavailable" or "Payment Failed" errors
- Database CPU at 100% for 15-20 minutes
- Query queue backs up to 50,000+ pending queries
- Legitimate bookings mixed with bot traffic (30% are bots/scalpers)
- No rate limiting per user (users script multiple parallel requests)
- Payment gateway charged but booking not confirmed (reconciliation nightmare)
- Revenue loss: ₹50 crore per month from failed bookings

#### Issue 2: Seat Allocation Race Conditions
- Two users simultaneously book last available seat
- Both see "seat available" in search results
- Both proceed to payment
- Both payments successful
- System confirms both bookings for same seat
- Database row-level locks not working correctly
- Happens 500-1000 times per day
- Manual intervention required to resolve
- Passengers show up at station with duplicate seat assignments
- Customer support overwhelmed with complaints
- Fraud opportunities: users deliberately exploit race condition

#### Issue 3: Search Service Breakdown
- Train search during festival season takes 30-60 seconds
- Database query scans 50 million schedule records
- No proper indexing on source-destination-date combination
- Full table scans on every search
- Caching exists but invalidation is broken
- Users see trains that no longer run on that route
- Fare displayed in search differs from booking page
- During peak hours, search service crashes completely
- 60% of users abandon booking due to slow search
- Search for "Mumbai to All Stations" crashes service (no pagination limit)

#### Issue 4: Payment Gateway Chaos
- Payment Service integrates with 15 different payment gateways
- Each gateway has different response times (2-30 seconds)
- No circuit breaker pattern implemented
- When one gateway is down, all payments routed to it fail
- No automatic failover to healthy gateways
- Seat blocked for 15 minutes waiting for payment confirmation
- If payment succeeds but network fails, booking lost
- Duplicate payment charges common (user retries, both go through)
- Refund reconciliation takes 7-14 days
- Payment status checking happens via polling (every 2 seconds for 15 minutes)
- Database overloaded with payment status queries

#### Issue 5: Waitlist Management Inefficiency
- Waitlist processed sequentially, one passenger at a time
- For popular trains, waitlist has 1000+ passengers
- When seat becomes available (cancellation), processing takes 5-10 minutes
- Locks entire train's booking table during processing
- New bookings blocked while waitlist is being processed
- Waitlist position calculation requires scanning all bookings
- No real-time waitlist position updates
- Passengers receive notification hours after confirmation
- RAC (Reservation Against Cancellation) logic even more complex
- Berth sharing rules not properly implemented

#### Issue 6: Chart Preparation Bottleneck
- Chart preparation is monolithic batch job
- Processes all trains sequentially (12,000 trains)
- Takes 6-8 hours to complete (supposed to finish in 4 hours)
- If job fails, must restart from beginning
- During chart preparation, bookings frozen for affected trains
- Last-minute bookings rejected
- Chart PDFs generated synchronously (CPU intensive)
- PDF service crashes for trains with 2000+ passengers
- No notification to conductor if chart generation fails
- Railway staff manually prepare charts (error-prone)

#### Issue 7: PNR Status Query Overload
- PNR status checked 500 million times per day
- 80% of queries are for same 1000 PNRs (popular trains)
- Each query hits Oracle database
- No caching of PNR status
- During train journey, users check status every 10 minutes
- Real-time train location not integrated with PNR status
- PNR database query joins 15 tables
- Response time: 3-8 seconds during peak
- Third-party apps scrape PNR data (DDoS-like traffic)
- API rate limiting not enforced

#### Issue 8: Real-Time Train Tracking Failure
- Train location updated via GPS every 5 minutes
- Location data sent to central server via 2G/3G network
- 30-40% of location updates lost (network issues)
- Train location shown on map is 20-30 minutes stale
- No prediction of delays or arrival times
- Platform allocation not synced with train location
- Passengers miss trains due to incorrect platform info
- No integration between tracking system and booking system
- When train is late, waitlist passengers not auto-confirmed
- Manual process to handle delay-related booking changes

#### Issue 9: Dynamic Pricing Chaos
- Dynamic pricing based on "demand" but calculation is flawed
- Queries last 1 hour's bookings to determine demand
- Query scans millions of records (no time-based indexing)
- Pricing calculation takes 5-10 seconds
- During calculation, prices can change
- User sees ₹1000 in search, ₹1500 at payment
- No price guarantee mechanism
- Surge pricing during Tatkal leads to 5x-10x normal fare
- Pricing service becomes bottleneck (single instance)
- No caching of fare calculations

#### Issue 10: Cancellation and Refund Delays
- Cancellation triggers complex refund calculation
- Rules based on time before departure (sliding scale)
- Queries booking, payment, fare rules (4-5 tables)
- Cancellation charge calculation is synchronous
- Refund amount sent to Refund Service
- Refund Service queues refunds for batch processing
- Batch runs once per day at midnight
- Refunds take 7-14 days to reach customer
- No instant refund option
- Failed refunds require manual intervention
- Refund status not visible to customer

#### Issue 11: Multi-Channel Booking Conflicts
- Same seat can be booked via multiple channels simultaneously
- Website, mobile app, agents, counters all use different service instances
- No distributed locking mechanism
- Database-level locks timeout due to network latency
- Counter bookings get priority (hardcoded logic)
- Online bookings often fail even when seats show available
- Quota distribution across channels is static (not dynamic)
- Agent quota exhausted but general quota has seats (no rebalancing)
- Channel-wise reporting is inconsistent

#### Issue 12: SMS/Email Notification Overload
- Notification Service sends 50 million SMS/emails per day
- Each booking triggers 4-5 notifications:
  - Booking confirmation
  - Payment confirmation
  - Chart preparation
  - Journey reminder
  - PNR status update
- Notifications sent synchronously (blocks booking flow)
- SMS gateway rate limited (10,000 SMS/minute)
- During peak, notification queue backs up to 2 million messages
- Critical notifications (cancellations, refunds) lost in queue
- No priority queue for urgent notifications
- Email delivery takes 6-12 hours during peak

#### Issue 13: Legacy System Integration Nightmare
- Freight operations run on 40-year-old mainframe
- Mainframe accessed via batch file transfers (overnight sync)
- No real-time integration
- Passenger trains and freight trains share tracks
- Track allocation conflicts not detected in real-time
- Mainframe can't handle REST APIs
- Data format: fixed-width text files
- Integration errors discovered next day
- Manual reconciliation required (20-30 staff dedicated)

#### Issue 14: Station and Platform Management Chaos
- Platform allocation done manually by station master
- No automation or optimization
- Platform changes not communicated to passengers in real-time
- Digital displays show incorrect information (cached data)
- Mobile app doesn't reflect platform changes
- During foggy weather (delays), entire system breaks down
- No dynamic rescheduling of trains
- Cascading delays not predicted
- Crew rostering not integrated with train delays
- Crew missing trains (no backup crew allocation)

#### Issue 15: Fraud and Bot Detection Failure
- 30-40% of Tatkal bookings are by bots/scalpers
- Bots use multiple accounts, VPNs, distributed attacks
- Captcha easily bypassed by OCR services
- No behavioral analysis or anomaly detection
- Scalpers book tickets and resell at 3x-5x price
- Legitimate passengers can't book tickets
- Bot detection is rule-based (IP blocking, rate limiting)
- Rules easily circumvented
- No machine learning-based fraud detection
- Revenue loss: ₹500 crore per year to black market

#### Issue 16: Disaster Recovery and Failover Issues
- DR site in Mumbai has 4-hour RPO, 8-hour RTO
- No active-active setup
- During datacenter outage, entire booking system down
- Manual failover process takes 2-3 hours
- Data replication is asynchronous (risk of data loss)
- DR site not tested regularly
- Last DR drill was 3 years ago (failed)
- No geographic distribution of services
- Single point of failure for critical services

