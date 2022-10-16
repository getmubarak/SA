

<ul>
<li>Users should be able to sign up using their email addresses and subscribe to a premium plan. If they don’t subscribe, they will get 1 GB of free storage.</li>
<li>Users should be able to upload and download their files from any device.</li>
<li>Users should be able to share files and folders with other users.</li>
<li>Users should be able to upload files up to 1 GB.</li>
<li>The system should support automatic synchronization across the devices.</li>
<li>The system should support offline editing. Users should be able to add/delete/modify files offline, and once they come online, changes should be synchronized to remote servers and other online devices.</li>
<li>ACID-ity is required: Atomicity, Consistency, Isolation, and Durability of all file operations should be guaranteed.</li>
</ul>


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
