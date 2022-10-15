### 1. Context
Uber aims to make transportation easy and reliable.  Uber enables customers to book affordable rides with drivers in their personal cars. The customers and drivers communicate using the Uber app.

### 2. Functional Requirements

#### Features for Uber App for Riders
- Track a Ride
Uber cab provides an option for its passenger to track a ride after they have booked their ride. What happens is, after the rider enters the pickup and drop location, the driver accepts the ride request through his uber app and approaches to the pickup location. To find out how far the driver is from the location, the passengers can track them by using the map integrated into the uber app for riders. Uber like apps are perfect for monitoring the journey and this makes for a unique value proposition.

- Fare Estimation
The passengers are able to draw a fare estimate for their ride on the basis of their pickup and drop location.
The fare also varies as per the selection of the car for the ride. When the passenger stops at multiple destinations in between his pickup and drop location, it gets calculated at the end of the ride with the help of the powerful Uber algorithm. Uber like app development, therefore, needs to focus on this feature.

- Multiple Modes of Payment
For making the taxi-hailing ecosystem user-friendly for the customers, Uber provides multiple modes of payment to choose for paying the fare. In the uber app for riders, passengers can select any type of payment, e.g. credit card, debit card, cash, mobile wallets, etc. offering a wide range of payment gateways is the key to an effective Uber like app.

- Track Service History
For the passengers who commute on daily basis, Uber has a feature called Track service history. With the help of service history, the passengers can get details about their rides in a specific period. The passengers can view any dates and the entire service history details will be available for them in the form of a report in the Uber for riders. Uber like app development should, therefore, focus on tracking service history.

- Book Now Ride later
Book Now Ride Later is an advanced feature of Uber. It allows the passengers to schedule their rides before the actual time of the ride. Once done, the passengers get confirmation. The passengers get the driver’s details before an hour of their scheduled ride and they can track the ride.

- Book for Others
Similar to Book Now Ride Later, Book for others is an advanced feature for the Uber app for riders. The passengers can book a ride for their friends and families by using their own account. After the booking is made, the passenger gets all the details about the ride and an SMS is delivered to the rider. Here, the tracking can also be done by using the link present in the SMS.

- Smart Wallets
Uber provides a smart wallet to its passengers for paying the fare. The passengers integrate these smart wallets with their bank accounts and transfer a certain amount of money. The passengers can directly make their payments using the mobile wallets. Uber like apps or Uber clones must incorporate this feature.

- Panic Button
To ensure the security of the passengers, Uber has taken measures in the form of a panic button. As soon as the passenger is on-board, a panic button gets enabled in the Uber app for riders. Uber clones should have such safety and security features in place.  If you’re wondering how to make an app like Uber, remember safety is a huge concern for cab passengers nowadays.

When the passengers feel threatened or sense danger, they can press the panic button in the Uber taxi app. This sends a notification to the nearest police station, the Uber authorities, and the family members of the passengers. The Uber cab app, therefore, considers every aspect.

- Favourite Destinations
The favorite destination is an advanced feature for the Uber rider app. When the passengers have to travel to the same destinations day-in and day-out, they can enter the destinations for once and can select it using a single tap, through the Uber app. The passengers can save destinations for their home, office, restaurants, etc. using the Uber app.

- Split Charges
This is an advanced feature for the Uber for riders. When the passengers are traveling with their friends, they can split their fare and pay individually on the basis of the charge of the ride and the pickup and drop location of each passenger. They can split their fare and if they have leveraged mobile wallets, the fare automatically gets deducted from the wallet. So the Uber like app should consider such features.


#### Features of the Uber App for Drivers
- Driver Delivery Reports
In order to ensure the safety of the passengers as well as the drivers, Uber for drivers has a delivery report feature. The report is a summary of the driving style of the driver during the entire week, month, etc. If the driver continues to drive rash, Uber can even remove the driver from the service, all thanks to the Uber cab app. On the other hand, if a driver drives smoothly, he becomes the trainer for the newbie Uber drivers. An Uber like app must have such features, too.

- Route optimization:
Route optimization helps the driver take the most efficient route so that they can reach the destinations in the fastest possible time. The driver can leverage the route optimization feature, reroute the entire journey and can navigate efficiently to the path, using the Uber taxi app.

- Driver Destinations:
The driver destination is an advanced feature in the Uber app for drivers. The driver can choose to take a ride to his preferred destination. This feature can be used by the drivers when they want to make money and have to reach their destinations, through the Uber cab app.

- Quest earnings:
Quest earning is a feature in the Uber for drivers. It helps the driver earn extra money. The quest comes with a pre-defined number of rides that the drivers have to complete in order to win and earn the additional amount.

- Shorter 2 Minute Cancellation Window:
The cancellation window is the time that the driver has to wait for the passenger. It is an advanced feature in the app for Uber drivers. When the driver arrives at the location on time, the first 2 minutes are non-chargeable for the passengers. However, if they take a lot of time, the charging starts and the passengers have to pay for that extra time as well along with the base fare of the ride.

- Heat Maps
Heat maps is the advanced feature in an ‘Uber like’ app. It is like a cheat sheet for the drivers. Heat maps is basically a map view of the demand. The drivers can know where the ratio of the passengers is high and can move to that location to get requests easily from the passengers.

- Forward Dispatch
The forward dispatch is an advanced feature for the Uber app for the driver. It allows the drivers to accept the request for another ride while they are still completing their current ride. This helps them to cut down the ideal time and earn a few more extra bucks.

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
