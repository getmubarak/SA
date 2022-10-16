
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

