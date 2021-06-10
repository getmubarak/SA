![alt text](https://github.com/getmubarak/SA/blob/master/views/Data%20View/Cloud%20Storage.png)

Virtual server disks 
databases management
* Azure Disks
* EBS
* Google Persistent Disk 
Workload
	Records are frequently created and updated.
	Multiple operations have to be completed in a single transaction.
	Relationships are enforced using database constraints.
	Indexes are used to optimize query performance.
Data type
	Data is highly normalized.
	Database schemas are required and enforced.
	Many-to-many relationships between data entities in the database.
	Constraints are defined in the schema and imposed on any data in the database.
	Data requires high integrity. Indexes and relationships need to be maintained accurately.
	Data requires strong consistency. Transactions operate in a way that ensures all data are 100% consistent for all users and processes.
	Size of individual data entries is small to medium-sized.
Examples
	Inventory management
	Order management
	Reporting database
	Accounting


Shared files (Block Storage)
Lift-and-shift application support
Analytics for big data
Content management system and web server support
* Azure Files
* EFS
* Google Filestore

 Object Storage
 Data lake and big data analytics
 Backup and restoration
 Reliable disaster recovery
* Azure Blobs
* S3
* google Cloud Storage

Archiving and backup
* Azure Archive Blob Storage
* AWS Glacier storage service
* Google Archival Cloud Storage.

Relation Database
* Azure SQL, MySql, Postgres Database
* Aws RDS
* GCP Cloud SQL

Key Value Storage
* Azure Table Storage
* Cosmos 
* SimpleDB
* Google Cloud Datastore

Document Storage
* Cosmos 
* Dynamo DB
* Amazon DocumentDB

Graph Storage
for data that looks like a network
Social relationships (nodes are people)
Public transport links (nodes can be bus or train stations)
Roadmaps (nodes are street intersections or highway intersections)
Anything requiring traversing a graph to find the shortest routes, nearest neighbors, etc.)
* Cosmos 
* Amazon Neptune
