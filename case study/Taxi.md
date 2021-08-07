# Scenario: 
A taxi company collects data about each taxi trip. 
For this scenario, we assume there are two separate devices sending data. 
The taxi has a meter that sends information about each ride — the duration, distance, and pickup and dropoff locations. 
A separate device accepts payments from customers and sends data about fares. 
The taxi company wants to calculate the average tip per mile driven, in real time, in order to spot trends.

# Data ingestion
To simulate a data source, use the New York City Taxi Data dataset.
https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797
This dataset contains data about taxi trips in New York City over a four-year period (2010–2013). It contains two types of record: ride data and fare data. Ride data includes trip duration, trip distance, and pickup and dropoff location. Fare data includes fare, tax, and tip amounts. Common fields in both record types include medallion number, hack license, and vendor ID. Together these three fields uniquely identify a taxi plus a driver. The data is stored in CSV format.

# Constraints
@ The taxi company may send the data in realtime or batched hourly/daily/weekend.
@ The product will be used by multiple taxi companies. The solution should be multi tenant.
@ Data from one tenant should never be exposed to other tenants

# Expectation
Please prepare for a 10-15 minute chalk talk to present a first nice cute of the following views
## Requirements
1. Context view
2. functional view
3. Quality View
4. Constraints
## Definition
1. Logical View
2. Data View
3. Deployment View
4. Infrastructure View
