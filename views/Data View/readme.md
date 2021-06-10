![alt text](https://github.com/getmubarak/SA/blob/master/views/Data%20View/Cloud%20Storage.png)

# Virtual server disks 
## Providers
- Azure Disks 
- EBS
- Google Persistent Disk 

# Shared files (Block Storage)
## Providers 
- Azure Files
- EFS
- Google Filestore
## Examples
- Lift-and-shift application support
- Analytics for big data
- Content management system and web server support

# Object Storage 
## Providers 
- Azure Blobs
- S3
- google Cloud Storage
## Workload
- Identified by key.
- Content is typically an asset such as a delimiter, image, or video file.
- Content must be durable and external to any application tier.
## Data type
- Data size is large.
- Value is opaque.
## Examples
- Images, videos, office documents, PDFs
- Static HTML, JSON, CSS
- Log and audit files
- Database backups
- Data lake and big data analytics
- Reliable disaster recovery

# Relation Database
## Providers 
- Azure SQL, MySql, Postgres Database
- Aws RDS
- GCP Cloud SQL
## Workload
- Records are frequently created and updated.
- Multiple operations have to be completed in a single transaction.
- Relationships are enforced using database constraints.
- Indexes are used to optimize query performance.
## Data type
- Data is highly normalized.
- Database schemas are required and enforced.
- Many-to-many relationships between data entities in the database.
- Constraints are defined in the schema and imposed on any data in the database.
- Data requires high integrity. Indexes and relationships need to be maintained accurately.
- Data requires strong consistency. Transactions operate in a way that ensures all data are 100% consistent for all users and processes.
- Size of individual data entries is small to medium-sized.
## Examples
- Inventory management
- Order management
- Reporting database
- Accounting
		
# Key Value Storage
## Providers 
- Azure Table Storage
- Cosmos 
- SimpleDB
- Google Cloud Datastore
## Workload
- Data is accessed using a single key, like a dictionary.
- No joins, lock, or unions are required.
- No aggregation mechanisms are used.
- Secondary indexes are generally not used.
## Data type
- Each key is associated with a single value.
- There is no schema enforcement.
- No relationships between entities.
## Examples
- Data caching
- Session management
- User preference and profile management
- Product recommendation and ad serving

# Document Storage
## Providers 
- Cosmos 
- Dynamo DB
- Amazon DocumentDB
## Workload
- Insert and update operations are common.
- No object-relational impedance mismatch. Documents can better match the object structures used in application code.
- Individual documents are retrieved and written as a single block.
- Data requires index on multiple fields.
## Data type
- Data can be managed in de-normalized way.
- Size of individual document data is relatively small.
- Each document type can use its own schema.
- Documents can include optional fields.
- Document data is semi-structured, meaning that data types of each field are not strictly defined.
## Examples
- Product catalog
- Content management
- Inventory management

# Graph Storage
## Providers 
- Cosmos 
- Amazon Neptune
## Workload
- Complex relationships between data items involving many hops between related data items.
- The relationship between data items are dynamic and change over time.
- Relationships between objects are first-class citizens, without requiring foreign-keys and joins to traverse.
## Data type
- Nodes and relationships.
- Nodes are similar to table rows or JSON documents.
- Relationships are just as important as nodes, and are exposed directly in the query language.
- Composite objects, such as a person with multiple phone numbers, tend to be broken into separate, smaller nodes, combined with traversable relationships
## Examples
- for data that looks like a network
- Social relationships (nodes are people)
- Public transport links (nodes can be bus or train stations)
- Roadmaps (nodes are street intersections or highway intersections)
- Anything requiring traversing a graph to find the shortest routes, nearest neighbors, etc.)
- Organization charts
- Social graphs
- Fraud detection
- Recommendation engines


# Archiving and backup
## Providers 
- Azure Archive Blob Storage
- AWS Glacier storage service
- Google Archival Cloud Storage.
