1. User Request (Client-Side - Passenger App):
* Open App & Set Destination: The user opens the Uber app, which detects their current GPS location. They then input their desired destination.
* Ride Options & Fare Estimation: The app displays available ride types (e.g., UberX, UberXL, Black, etc.) along with estimated fares and arrival times for each. This involves:
    * Fare Calculation Service: Takes into account distance, estimated time, surge pricing, traffic conditions, tolls, and ride type.
    * ETA Service: Calculates estimated time of arrival based on current traffic, driver availability, and route.
* Confirm Booking: The user selects their preferred ride option and taps "Confirm."

2. Ride Request Processing (Backend - Core Services):

API Gateway/Load Balancer: The request hits Uber's backend through an API Gateway, which routes it to the appropriate services.
Request Validation: The request is validated (e.g., valid user, payment method, destination).
Geospatial Matching Service: This is a critical component. It performs the following:
Driver Discovery: Identifies available drivers in the vicinity of the passenger's pickup location. This is often done using spatial indexing techniques (e.g., S2 library, geohashing) to quickly query for drivers within a certain radius.
Driver Filtering: Filters drivers based on:
Availability (online, not on a trip)
Vehicle type
Rating
Proximity
Acceptance rate
Destination (if destination filtering is enabled for drivers)
Dynamic Pricing/Surge Calculation (if not already done): If surge pricing is in effect, it's applied here based on real-time supply and demand in the area.
Matchmaking Algorithm: This is the core logic. Uber uses sophisticated algorithms to match the passenger with the "best" available driver. Factors considered include:
Proximity: Closest available driver.
ETA: Driver who can reach the passenger fastest.
Driver's Current Trajectory: If a driver is already moving in the direction of the passenger, they might be preferred.
Fairness: Ensuring drivers get a reasonable share of requests.
Pre-existing Queues/Zones: Drivers might be queued in certain zones.
Driver Notification Service: Once a potential match is found, a notification is sent to the selected driver's app.
3. Driver Acceptance (Client-Side - Driver App):

Notification Received: The driver's app receives a ride request notification, displaying the passenger's pickup location, destination, estimated earnings, and pickup time.
Accept/Decline: The driver has a limited time to accept or decline the ride.
Accept: If the driver accepts, the ride is confirmed.
Decline/Timeout: If the driver declines or the request times out, the matchmaking service will attempt to find another suitable driver.



### 3. Quality Requirements
- 300 million customers and one million drivers in the system
- One million active customers and 500 thousand active drivers per day
- One million rides per day
- All active drivers notify their current location every three seconds
- System contacts drivers in real time when customer puts in a ride request
- driver’s current and previous location :  35 bytes.
DriverID (3 bytes for 1 million drivers)
Old latitude (8 bytes)
Old longitude (8 bytes)
New latitude (8 bytes)
New longitude (8 bytes)
- Since we assume one million drivers, we’ll require the following memory:
 1 million ∗ 35 bytes => 35MB
- we get the DriverID and location, it will require (3+16 => 19 bytes). This information is received every three seconds from 500 thousand daily active drivers, so we receive 9.5MB every three seconds.
- We need to store both driver and customer IDs. We need three bytes for DriverID and eight bytes for CustomerID, so we will need 21MB of memory.
(500,000 * 3) + (500,000 * 5 * 8 ) ~= 21 MB

- Now for bandwidth. For every active driver, we have five subscribers. In total, this reaches:
5 * 500,000 => 2.5million5∗500,000=>2.5million

- We need to send DriverID (3 bytes) and their location (16 bytes) every second, which requires:
2.5million * 19 bytes => 47.5 MB/s2.5million∗19bytes=>47.5MB/s


### Reference
- https://highscalability.com/brief-history-of-scaling-uber/
- https://techtakshila.com/system-design-interview/chapter-3/
- https://www.educative.io/blog/uber-backend-system-design
- https://www.geeksforgeeks.org/system-design-of-uber-app-uber-system-architecture/
- https://medium.com/@narengowda/uber-system-design-8b2bc95e2cfe
- https://www.youtube.com/watch?v=fgIgOU0yi0w
- https://www.youtube.com/watch?v=Tp8kpMe-ZKw
- https://tianpan.co/notes/120-designing-uber
- https://github.com/puncsky/system-design-and-architecture/blob/master/en/120-designing-uber.md
- https://www.codekarle.com/system-design/Uber-system-design.html


#### Services

- Payment Service
- Cab Finder
- Trip Service
- Rider Service
- Location Service (Segment)
- Map Service  
- Fraud Detection Service
- Route Service
- Fare Service
- Notification Service
