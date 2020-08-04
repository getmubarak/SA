Quality Attribute	| Performance	 
------------------|-------------
Source |	A hardware or software component	
Stimulus	| fails	
Artifact |	In the OPC System.	
Environment |	When no backup System is available.  A copy of the component is available on persistent storage and can be transferred to the spare processor via the LAN.	
Response	| "The clients are informed that the service has become  Unavailable . A new copy of the service is started and becomes operational. The state of the component on restart may differ from that of the failed component but by no more than one minute . The clients are then informed that the service is available for new queries."	
Response Measure	| The new copy is available within three minutes.	
