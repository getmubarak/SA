In data-centered architecture, the data is centralized and accessed frequently by other components, which modify data. The components access a shared data structure and they interact only through the data store.


Repository Architecture Style
In Repository Architecture Style, the data store is passive and the clients of the data store are active, which control the logic flow. The participating components check the data-store for changes. One of the most well-known examples of the data-centered architecture, is a database architecture

The main difference between repository architecture and client-server architecture is that in repository architecture the centralized point has only the data where as in client-server architecture, the server has also some processing power. 


Blackboard Architecture Style
In Blackboard Architecture Style, the data store is active and its clients are passive. BLACKBOARD is a specialised variant of SHARED REPOSITORY  A more generic version of a message queue. The blackboard pattern generalizes the observer. It completely decouples producers and consumers of information.

