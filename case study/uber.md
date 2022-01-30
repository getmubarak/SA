### 1. Context
Uber aims to make transportation easy and reliable.  Uber enables customers to book affordable rides with drivers in their personal cars. The customers and drivers communicate using the Uber app.

### 2. Functional Requirements
- Drivers must be able to frequently notify the service regarding their current location and availability. Every cab which is active keep on sending lat-long to the server every 5 sec once
- Passengers should be able to see all nearby drivers in real-time
- Customers can request a ride using a destination and pickup time.
- Nearby drivers should be notified when a customer needs to be picked up.
- Once a ride is accepted, both the driver and customer must see the other’s current location for the entire duration of the trip.
- Once the drive is complete, the driver completes the ride and should then be available for another customer.

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
- https://techtakshila.com/system-design-interview/chapter-3/
- https://www.educative.io/blog/uber-backend-system-design
- https://www.geeksforgeeks.org/system-design-of-uber-app-uber-system-architecture/
- https://medium.com/@narengowda/uber-system-design-8b2bc95e2cfe
- https://www.youtube.com/watch?v=fgIgOU0yi0w
- https://www.youtube.com/watch?v=Tp8kpMe-ZKw
- https://tianpan.co/notes/120-designing-uber
- https://github.com/puncsky/system-design-and-architecture/blob/master/en/120-designing-uber.md
- https://www.codekarle.com/system-design/Uber-system-design.html
