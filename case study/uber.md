### 1. Context
Uber aims to make transportation easy and reliable.  Uber enables customers to book affordable rides with drivers in their personal cars. The customers and drivers communicate using the Uber app.

### 1. Functional Requirements
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


