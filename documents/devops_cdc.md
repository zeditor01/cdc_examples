[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Devops Approaches for CDC

This paper has yet to be written. The following is an indication of the nature of the content that it will address.

* The scope of this paper is to examine the most productive way to administer and operate CDC on an ongoing basis.
* It is less concerned with the initial deployment logistics and more concerned with ongoing operations, and change control.

***Operations*** is more straightforward.

CDC health is relatively easy to discern because if you can verify the status of all subscriptions in "Up" 
and the latency of all subscriptions lower than a pre-determined health threshold (say 60 seconds) 
then that's a pretty good approximation to a "green light" health system outcome.

In the event that any CDC subscriptions fail to pass the "healthy" thresholds, the Management Console provides an 
easy to use drill down into the specific subscriptions that have an unhealthy status, view operational diagnostics from both source and 
target ends of the subscription, and drill down into the problem

The health determination step is easily automated.
The problem determination step starts with the easy to use Management Console, and progresses to ssh sessions to diagnose and resolve deeper problems on specific CDC servers.

***Change Control*** is more challenging

The nirvana of database replication systems is that whenever a change is made on the source database, you should be able to configure the replication system to filter that source system change all the way through the cdc subscriptions, and into the target database without taking any outage. This is sometimes referred to as "DDL Replication".

IBM Infosphere Data Replication actually implements this nirvana state fairly comprehensively for some homogeneous data replication environments 
(e.g. DB2 z/OS to Db2 z/OS ; Oracle to Oracle )

DDL Replication is not yet a reality for heterogeneous replication scenarios. Generally speaking, clients that are replicating data from z/OS source systems (IMS, VSAM and Db2 z/OS) are typically replicating to targets like Db2 for Linux, SQL Server, Oracle or (increasingly frequently) Kafka. So, efficient procedures (with minimal interuption into application availability) are needed to handle source database changes and incorporate those changes into the replication subscriptions and on to the target databases.


A number of scenarios will be considered
1. What changes can be made to a source database with and without impacting downstream replication subscriptions.
2. If the change needs to be propagated to the target, what operational procedures exist to allow that to happen, and with what outages to what applications ?
3. Some template patterns to avoid any application outage.

The "template patterns" will be dependant on source and target. An example of such a template pattern might be.
1. Plan the changes in advance (eg: Add a brand new table to the source schema, and add 2 new columns to an existing table in the source schema)
2. Develop a non-intrusive change to source database and target database (new columns will allow NULLS, or be NNWD)
3. Stop the subscription(s) (without ceasing source operations, but maintaning the replication bookmark).
4. Deploy the non-intrusive change to both source and target
5. Re-map the subscription to include the new and changed objects
6. Resume the subscription
7. Deploy the new version of the source application(s) that are able to write data to the new objects.


***Operational Co-ordination*** needs to consider the number of different teams that need to be involved in the implementation of changes like this.

1. Source DBA
2. Target DBA
3. Source Replication Admin
4. Target Replication Admin
5. etc...

Adopting a Shift-Left or a Shift-Right CDC deployment model has the potential to remove one of the CDC teams,

Allowing CDC to generate DDL automatically for the target database is another potential efficiency. However, for large or performance-critical objects you would want the DBA to specify the DDL manually.


