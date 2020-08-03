What is the expected response time for each use case?
What is the average/max/min expected response time?
What resources are being used (CPU, LAN, etc.)?
What is the resource consumption?
What is the resource arbitration policy?
What is the expected number of concurrent sessions?
Are there any particularly long computations that occur when a certain state is true?
Are server processes single or multithreaded?
Is there sufficient network bandwidth to all nodes on the network?
Are there multiple threads or processes accessing a shared resource? How is access managed?
Will bad performance dramatically affect usability?
Is the response time synchronous or asynchronous?
What is the expected batch cycle time?
How much can performance vary based on the time of day, week, month, or system load?
What is the expected growth of system load?
Increased network bandwidth consumption? 
Increased database server processing, resulting in reduced throughput. 
Increased memory consumption, excessive cache misses
Increased client response time, reduced throughput, and server resource over utilization. 
