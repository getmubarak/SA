Portion of Scenario	| Possible Values
--------------------|------------------------
Source	| Internal to the system; external to the system
Stimulus	| Duplicate Request, Fault; omission, crash, timing, response
Artifact |	System’s processors, communication channels, persistent storage, processes
Environment	| Normal operation; Failure mode
Response	| The system recovers in a correct and consistent way, data remains in a consistent state, notified,
Response Measure	| Mean Time to Failure (MTTF), Mean Time to Repair (MTTR), Mean Time Between Failure (MTBR),Probability of Failure  (POF)


## Mean Time to Failure (MTTF)
MTTF is described as the time interval between the two successive failures. An MTTF of 200 mean that one failure can be expected each 200-time units. The time units are entirely dependent on the system & it can even be stated in the number of transactions.


## Mean Time to Repair (MTTR)
Once failure occurs, some-time is required to fix the error. MTTR measures the average time it takes to track the errors causing the failure and to fix them.

## Mean Time Between Failure (MTBR)
MTBF = MTTF + MTTR

## Probability of Failure  (POF)
described as the probability that the system will fail when a service is requested. A POF of 0.1 means that one out of ten service requests may fail.
