* Is there a clear physical architecture?
* What hardware does this include across all tiers?
* Does it cater for redundancy, failover and disaster recovery if applicable?
* Is it clear how the chosen hardware components have been sized?
* If multiple boxes and sites are used, what are the network links between them?
* Who is responsible for support and maintenance of the infrastructure?
* Are there central teams to look after common infrastructure (e.g. databases, message buses, application servers, networks, routers, switches, load balancers, reverse proxies, internet connections, etc)?
* Who owns the resources?
* Are there sufficient resources for development, testing, acceptance, pre-production, production, etc?
* Is it clear how the software components will be deployed across the hardware elements described in the physical view? (e.g. one-to-one mapping, * multiple software components per server, etc)
* If this is still to be decided, what are the options and have they been documented?
* Is it understood how memory and CPU will be partitioned between the processes running on a single hardware node?
* Which components are active-active and which are active-passive?
* Which components can be scaled-out?
* Is it clear how data is replicated across sites?
* Has the rollout and recovery strategy been defined (this might be in a separate document, but referenced)?
* How are the components installed and configured?

