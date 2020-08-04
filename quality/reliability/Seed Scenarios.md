Quality Attribute |	Reliability	
------------------|---------------
Source |	System failure in the OPC	
Stimulus	| The OPC sends a request to the bank to charge a credit card for a purchased travel package; before receiving the reply from the bank, the OPC crashes.	
Artifact	| OPC and bank service	
Environment	| Failure mode	
Response	| The system recovers in a correct and consistent way, and the credit card is charged only once.	
Response Measure | In 100% of the cases	

Quality Attribute |	Reliability	
------------------|---------------
Source	| External to system	
Stimulus	| The Consumer Web site sent a purchase order request to the OPC. The OPC processed that request but didn’t reply to Consumer Website within five seconds, so the Consumer Web site resends the request to the OPC.	
Artifact	| Adventure Builder system	
Environment	| Normal operation	
Response |	"The OPC receives the duplicate request, but the consumer is not double-charged, data remains in a consistent state, and the Consumer Web site is notified that the original request was successful."	
Response Measure	| In 100% of the cases	
