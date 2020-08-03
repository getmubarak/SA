"robustness" describes an application's response to its input  while "fault-tolerance" describes an application's response to its environment.

An app is robust when it can work consistently with inconsistent data. A robust program will accept junk input and not crash. A music player is robust when it can continue decoding an MP3 after encountering a malformed frame. A maps application is robust when it can parse addresses in various formats with various misspellings and return a useful location. 

An app is fault-tolerant when it can work consistently in an inconsistent environment. A database application is fault-tolerant when it can access an alternate shard when the primary is unavailable. A web application is fault-tolerant when it can continue handling requests from cache even when an API host is unreachable. A storage subsystem is fault-tolerant when it can return results calculated from parity when a disk member is offline.

The opposite of robust code is fragile code. 

program that accepts "pancakes" for a date input and pops up a error box. 

The harder it is to create an error of any type or form that the computer cannot handle safely the more robust the software is.

If you send a bad HTTP header to a robust web server, it shouldn't crash.

If a robust web server runs for a very long time, its memory footprint should stay the same.

there are many types of failures: incorrect code, incomplete code, unexpected values, unexpected states, exceptions, resource exhaustion,… Robust code handles these well.

Java manages all dynamic memory, checks array bounds, and other exceptions.
