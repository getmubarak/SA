## If Your Application Needs...
- complex transactions because you can't afford to lose data or if you would like a simple transaction programming model then look at a Relational or Grid database.
- Example: an inventory system that might want full ACID. I was very unhappy when I bought a product and they said later they were out of stock. I did not want a compensated transaction. I wanted my item!
- to scale then NoSQL or SQL can work. Look for systems that support scale-out, partitioning, live addition and removal of machines, load balancing, automatic sharding and rebalancing, and fault tolerance.
- to always be able to write to a database because you need high availability then look at Bigtable Clones which feature eventual consistency.
- to handle lots of small continuous reads and writes, that may be volatile, then look at Document or Key-value or databases offering fast in-memory access. Also consider SSD.
- to implement social network operations then you first may want a Graph database or second, a database like Riak that supports relationships. An in- memory relational database with simple SQL joins might suffice for small data sets. Redis' set and list operations could work too.

## If Your Application Needs...
- to operate over a wide variety of access patterns and data types then look at a Document database, they generally are flexible and perform well.
powerful offline reporting with large datasets then look at Hadoop first and second, products that support MapReduce. Supporting MapReduce isn't the same as being good at it.
- to span multiple data-centers then look at Bigtable Clones and other products that offer a distributed option that can handle the long latencies and are partition tolerant.
- to build CRUD apps then look at a Document database, they make it easy to access complex data without joins. 
built-in search then look at Riak.
- to operate on data structures like lists, sets, queues, publish-subscribe then look at Redis. Useful for distributed locking, capped logs, and a lot more.
programmer friendliness in the form of programmer friendly data types like JSON, HTTP, REST, Javascript then first look at Document databases and then Key-value Databases.

## If Your Application Needs...
- transactions combined with materialized views for real-time data feeds then look at VoltDB. Great for data-rollups and time windowing.
- enterprise level support and SLAs then look for a product that makes a point of catering to that market. Membase is an example.
- to log continuous streams of data that may have no consistency guarantees necessary at all then look at Bigtable Clones because they generally work on distributed file systems that can handle a lot of writes.
- to be as simple as possible to operate then look for a hosted or PaaS solution because they will do all the work for you.
- to be sold to enterprise customers then consider a Relational Database because they are used to relational technology.
- to dynamically build relationships between objects that have dynamic properties then consider a Graph Database because often they will not require a schema and models can be built incrementally through programming.
to support large media then look storage services like S3. NoSQL systems tend not to handle large BLOBS, though MongoDB has a file service.

## If Your Application Needs...
- to bulk upload lots of data quickly and efficiently then look for a product supports that scenario. Most will not because they don't support bulk operations.
- an easier upgrade path then use a fluid schema system like a Document Database or a Key-value Database because it supports optional fields, adding fields, and field deletions without the need to build an entire schema migration framework.
- to implement integrity constraints then pick a database that support SQL DDL, implement them in stored procedures, or implement them in application code.
- a very deep join depth the use a Graph Database because they support blisteringly fast navigation between entities.
- to move behavior close to the data so the data doesn't have to be moved over the network then look at stored procedures of one kind or another. These can be found in Relational, Grid, Document, and even Key-value databases.

## If Your Application Needs...
- to cache or store BLOB data then look at a Key-value store. Caching can for bits of web pages, or to save complex objects that were expensive to join in a relational database, to reduce latency, and so on.
- a proven track record like not corrupting data and just generally working then pick an established product and when you hit scaling (or other issues) use on of the common workarounds (scale-up, tuning, memcached, sharding, denormalization, etc).
- fluid data types because your data isn't tabular in nature, or requires a flexible number of columns, or has a complex structure, or varies by user (or whatever), then look at Document, Key-value, and Bigtable Clone databases. Each has a lot of flexibility in their data types.
- other business units to run quick relational queries so you don't have to reimplement everything then use a database that supports SQL.
- to operate in the cloud and automatically take full advantage of cloud features then we may not be there yet.   

## If Your Application Needs...
- support for secondary indexes so you can look up data by different keys then look at relational databases and Cassandra's new secondary index support.
- creates an ever-growing set of data (really BigData) that rarely gets accessed then look at Bigtable Clone which will spread the data over a distributed file system.
- to integrate with other services then check if the database provides some sort of write-behind syncing feature so you can capture database changes and feed them into other systems to ensure consistency.
- fault tolerance check how durable writes are in the face power failures, partitions, and other failure scenarios.
- to push the technological envelope in a direction nobody seems to be going then build it yourself because that's what it takes to be great sometimes.
- to work on a mobile platform then look at CouchDB/Mobile couchbase.

- http://highscalability.com/blog/2011/6/20/35-use-cases-for-choosing-your-next-nosql-database.html
