[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

![Roadwork](/images/work_in_progress.jpg)

# Monitoring and CDC Subscriptions

This paper has yet to be written. The following is an indication of the nature of the content that it will address.

CDC health is relatively easy to discern because if you can verify the status of all subscriptions in "Up" 
and the latency of all subscriptions lower than a pre-determined health threshold (say 60 seconds) 
then that's a pretty good approximation to a "green light" health system outcome.

In the event that any CDC subscriptions fail to pass the "healthy" thresholds, the Management Console provides an 
easy to use drill down into the specific subscriptions that have an unhealthy status, view operational diagnostics from both source and 
target ends of the subscription, and drill down into the problem

The health determination step is easily automated.
The problem determination step starts with the easy to use Management Console, and progresses to ssh sessions to diagnose and resolve deeper problems on specific CDC servers.


This paper will review patterns for monitoring and alerting
* using Management Console
* using scripted approaches


