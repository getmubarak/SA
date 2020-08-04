# 1. Restate the following quality attribute requirement as a Quality Attribute Scenario (QAS).
If a processor in the “central system” (that is, the part of  BusTracker that is not on a bus or in a display) stops working during peak traffic hours then the system should notify the operator, system administrator, and manager, what happened and start providing “degraded mode” service (showing only scheduled arrival times for all buses). The time spent in degraded mode should be no more than 5 minutes. If a processor fails not more than once every other day, and takes not more than 5 minutes to get a new processor running (replace or reboot), then it will be available more than 99% of the time of peak traffic period.


# 2. Restate the following quality attribute requirement as a Quality Attribute Scenario (QAS).
If the bus subsystem stops transmitting its location for any reason (processor crashes, GPS receiver stops working, transmitter stops working) then the displays should show the scheduled arrival time for that bus. This will continue until the bus returns to its base, whereupon the subsystem should be ﬁxed (or replaced) within 20 minutes. Assuming 500 buses on road at any time, to get 99% of buses, cannot have more than 5 subsystems on active buses not working at any time. This assumes that there is 20 minutes before the bus needs to be put back on the road.


# 3. Restate the following quality attribute requirement as a Quality Attribute Scenario (QAS).
When a new display has been installed at a bus stop, it should start showing bus arrival information within 30 minutes of the request to do so by the Operations Manager. The Operations Manager will only make such a request outside of peak trafﬁc hours. New displays will only be brought on-line in off-peak hours and should not require signiﬁcant effort to be made operational once all of the equipment has been installed and tested.

# 4. Restate the following quality attribute requirement as a Quality Attribute Scenario (QAS).
The Consumer Web site sent a purchase order request to the XYZ Application. The XYZ processed that request but didn’t reply to Consumer Website within five seconds, so the Consumer Web site resends the request to the XYZ. The XYZ receives the duplicate request, but the consumer is not double-charged, data remains in a consistent state, and the Consumer Web site is notified that the original request was successful.

