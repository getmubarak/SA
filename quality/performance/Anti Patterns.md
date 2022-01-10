### Busy Database	
###### Offloading too much processing to a data store.

### Busy Front End	
###### Moving resource-intensive tasks onto background threads.

### Chatty I/O	
###### Continually sending many small network requests.

### Extraneous Fetching	
###### Retrieving more data than is needed, resulting in unnecessary I/O.

### Improper Instantiation	
###### Repeatedly creating and destroying objects that are designed to be shared and reused.

### Monolithic Persistence	
###### Using the same data store for data with very different usage patterns.

### No Caching	
###### Failing to cache data.

### Noisy Neighbor	
###### A single tenant uses a disproportionate amount of the resources.

### Retry Storm	
###### Retrying failed requests to a server too often.

### Synchronous I/O	
##### Blocking the calling thread while I/O completes.
