## Context -

We need to design a service like Google Drive or Dropbox which allows users to store their data securely, synchronised & effectively on remote servers. User should be able to download and upload files from all their devices. System should be highly available, reliable and scalable.

## Functional Requirements -

#### Users should be able to upload and download their files from any device.
#### Users should be able to share files and folders with other users.
#### File versioning should be able to restore the previous version of the file.
#### The system should support automatic synchronization across the devices.
#### The system should support offline editing. Users should be able to add/delete/modify files offline and once they come online, changes should be synchronized to remote servers and other online devices.
#### The system should support storing large files up to a GB. (Dropbox currently supports up to 50 GB)

## Scale
#### Total Users : ~100 M
#### Daily Active Users : ~50 M
#### QPS : ~500M request per day (~9000 Queries Per Second)
#### Storage Estimate : Assume, every user has avg 100 files (avg file size ~1MB) so we will have a total of 100B files. Hence, total storage would be -
100MB x 100B = 10000PB.

#### Read/Write ratio : ~ 1:1
#### Traffic Estimate : ~5 GB new file writes per second
#### Memory Usage : Assume, each user access 5 files daily and reading chunks of 200KB out of 1MB. So, following the 80-20 rule (80% traffic comes for 20% of the files), our cache size -
((50 M x 200KB x 5 ) x 20) / 100 = 10 Tera Byte
#### Active Connections : 1 M active connections per minute

