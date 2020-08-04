
* Call API with wrong number of parameters, with wrong data types, with values exceeding the allowed range, with special characters e.g. white spaces, control-sequences, characters beyond standard ASCII-range, …

* Submit more requests then the API can handle.

* How are the errors initiated, or triggered, in the real world?

* What types of corrective and recovery actions are required for each type of error?

* What kinds of disasters can strike?

* If you send a bad HTTP header to a robust web server, it shouldn't crash.

* If a robust web server runs for a very long time, its memory footprint should stay the same.

* there are many types of failures: incorrect code, incomplete code, unexpected values, unexpected states, exceptions, resource exhaustion,… Robust code handles these well.

* Java manages all dynamic memory, checks array bounds, and other exceptions.









