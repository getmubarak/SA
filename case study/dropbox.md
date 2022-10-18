
Dropbox is a cloud storage service that allows users to store their data on remote servers. The remote servers store files durably and securely, and these files are accessible anywhere with an Internet connection. Usually, these servers are maintained by cloud storage providers and made available to users over a network (typically through the Internet). Users pay for their cloud data storage.


### Rquirements
<ul>
<li>Users should be able to sign up using their email addresses and subscribe to a premium plan. If they don’t subscribe, they will get 1 GB of free storage.</li>
<li>Users should be able to upload and download their files from any device.</li>
<li>Users should be able to share files and folders with other users.</li>
<li>Users should be able to upload files up to 1 GB.</li>
<li>The system should support automatic synchronization across the devices.</li>
<li>The system should support offline editing. Users should be able to add/delete/modify files offline, and once they come online, changes should be synchronized to remote servers and other online devices.</li>
<li>ACID-ity is required: Atomicity, Consistency, Isolation, and Durability of all file operations should be guaranteed.</li>
</ul>

### Qualtiy Requirements
<ul>
<li>The total number of users = 500 million.</li>
<li>Total number of daily active users = 100 million</li>
<li>The average number of files stored by each user = 200</li>
<li>The average size of each file = 100 KB</li>
<li>Total number of active connections per minute = 1 million</li>
  <li> QPS : ~500M request per day (~9000 Queries Per Second)   </li>
<li>Total number of files = 500 million  * 200 = 100 billion</li>
<li>Total storage required = 100 billion * 100 KB = 10 PB</li>
</ul>

### References
- https://www.youtube.com/watch?v=h3vWyiRBZHc
- https://www.youtube.com/watch?v=U0xTu6E2CT8
- https://www.youtube.com/watch?v=3RHjRXWAUvg
- 

### Functional decomposition
#### watcher
- Watcher is responsible for monitoring the sync folder for all the activities performed by the user such as creating, updating, or deleting files/folders.
- It gives notification to the Indexer and chunker if any action is performed in the files or folders.

#### Chunker
- Chunker breaks the files into multiple small pieces called chunks and uploads them to the cloud storage with a unique id or hash of these chunks. 
- Any changes in the files, the chunking algorithm detects the specific chunk which is modified and only saves that specific part/chunk to the cloud storage.
- notifies Indexer about the chunks uploaded.

#### Indexer
- listens for the events from watcher and updates the Client Metadata Database with information about the chunks of modified file. It also notifies Synchronizer after committing changes to Client Metadata Database.
- It receives the URL of the chunks from the chunker along with the hash and updates the file with modified chunks. 

#### Client Synchronizer: 
- Synchronizer listens for events from Indexer and communicates with Meta Service and Block service for updating meta data and modified chunk of file on remote server respectively. 
- It also listens for changes broadcasted by Notification Service and downloads the modified chunks from remote server.

#### Client Metadata Database: 
- Client Metadata Database stores the information about different files in workspace, file chunks, chunk version and file location in the file system.

#### Cloud Block Service
- Block Service interacts with block storage for uploading and downloading of files. Clients connect with Block Service to upload or download file chunks.

#### Cloud Meta Service
Meta Service is responsible for synchronizing the file metadata from client to server. It’s also responsible to figure out the change set for different clients and broadcast it to them using Notification Service.

#### Cloud Notification Service
Notification Service broadcasts the file changes to connected clients making sure any change to file is reflected all watching clients instantly.

