## Description

The goal of this github respository is to provide practical worked examples of implementing IBM InfoSphere CDC solutions between mainframe, midrange and cloud systems. 
It contains  

* 16 documents that illustrate a range of tasks from installation through to operations and monitoring
* suppliemented with code samples for some of the  tasks.

![ZDIM](images/cdc/zdim.png)

## Contents

| Installing and Configuring CDC Agents | Using CDC |
| --- | --- |
| [1. Environment for CDC Worked Examples.](C001_environment.md) | [10. Creating and Operating CDC Subscriptions.](C010_administration.md) |
| [2. Setting up CDC for Db2 on z/OS.](C002_cdcdb2zos.md) | [11. Devops Options for CDC.](C011_devops.md) | 
| [3. Setting up Classic CDC for IMS.](C003_cdcims.md) | [12. CHCCLP Scripting.](C012_chcclp.md) |
| [4. Setting up Classic CDC for VSAM.](C004_cdcvsam.md) | [13. CDC Security - LDAP Authentication and Authorisation.](C013_LDAP.md) |
| [5. Setting up CDC for Kafka in zCX.](C005_zcx.md) | [14. CDC Security - TLS Encryption.](C014_TLS.md) |
| [6. Setting up CDC for Db2 on Linux.](C006_db2linux.md) | [15. Container Deployment.](C015_containers.md) |
| [7. Setting up CDC for Kafka.](C007_kafka.md) | [16. Monitoring and Managing outwith the Windows MC.](C016_dashboard.md)  |
| [8. Setting up remote CDC Capture for Db2 z/OS.](C008_rdb2zos.md) | [17. CDC Use-Case Suitability.](C017_use_cases.md)  |
| [9. Setting up remote CDC Capture for VSAM.](C009_rvsam.md) |     |    


Database replication is widely used by organisations to maintain copies of operational data on systems other than the primary source database. 

Twenty years ago the most common use case for database replication was to maintain a replica database supporting data analytics in a dedicated reporting environment. 
The justications for the effort and cost of replicating the data would typically include (a) protecting the operational service levels of the critical transaction systems and 
(b) making use of hardware which was optimised for analytics.

Nowadays the primary use case is to support digital integration hubs that serve data requests via Cloud APIs. As Enterprises seize upon the benefits of cloud computing, they 
are compiling sets of APIs that are required support their Cloud applications. These business-focussed APIs do not care about the boundaries that exist between siloed 
operational systems. Digital Integration Hubs are designed to curate API-Ready data from a range of heterogeneous source systems. They need maintain the 
currency of the curated data by consuming streams of change data from the source systems.

The focus of the worked examples in this publication are for mainframe databases such Db2 z/OS, IMS and VSAM. IBM InfoSphere CDC can capture changes from these source systems 
and publish them to targets such as Kafka, Db2 and Oracle.

Code samples for the demo application used in this Redpaper can be downloaded at [code sample](https://github.com/zeditor01/cdc_examples/tree/main/code%20sample).


