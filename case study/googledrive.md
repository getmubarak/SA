

Functional Requirements -

User should be able to download/upload, update, delete files from any device
Files should be synchronised in all the devices that the user is logged in
History/versioning of files (snapshotting of data)

Scale
Total Users : ~100 M
Daily Active Users : ~50 M
QPS : ~500M request per day (~9000 Queries Per Second)
Storage Estimate : Assume, every user has avg 100 files (avg file size ~1MB) so we will have a total of 100B files. Hence, total storage would be -
100MB x 100B = 10000PB.

Read/Write ratio : ~ 1:1
Traffic Estimate : ~5 GB new file writes per second
Memory Usage : Assume, each user access 5 files daily and reading chunks of 200KB out of 1MB. So, following the 80-20 rule (80% traffic comes for 20% of the files), our cache size -
((50 M x 200KB x 5 ) x 20) / 100 = 10 Tera Byte
Active Connections : 1 M active connections per minute

