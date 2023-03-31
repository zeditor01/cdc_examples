# Create, Scale and Sustain CDC data replication systems

## Abstract
This github repository is dedicated to addressing the practical aspects of implementing CDC data replication solutions, primarily 
through the use of documenting worked examples of how to build CDC solutions that are easy to operate, manage and maintain.

## The True nature of a heterogenous data replication project
Establishing CDC replication solutions in a "simple unconstrained environment" is fairly straightforward. 
* Install and configure the CDC components (capture agent, apply agent, admin tools)
* Define data replication subscriptions
* Start and monitor those subscriptions 

Simple huh ?

Enterprise customers do ***not*** have "simple unconstrained environments". 
The production environments where CDC must be deployed are highly evolved to provide well-secured, high-performance, 
continuously-available services for highly visible transactional systems that cannot tolerate service outages.
It is the critical nature of these production systems that is often the driving force for establishing data replicas on 
other systems for purposes such as analytics, data science, digital integration and so on. But the very practices that 
are established to protect these business-critical systems are the same practices that make the deployment of 
heterogeneous CDC replication solutions challenging.

The list below are just of the complexities that a typical CDC deployment will face is a real-world environment.
* Strict governance controls for database configuration changes
* Complex network paths to navigate (firewalls, jump boxes etc...).
* Requirement for network encryption for CDC data flows.
* Conformance to cross-platform authentication and authorisation standards (LDAP etc...)
* Understanding the differences between application-transparent TLS (z/OS) and application-controlled TLS (most other systems)
* different specialist groups for typical data sources ( IMS, Db2 z/OS, VSAM ) and typical data targets ( Kafka, SQL Server, Oracle )
* For Kafka targets (very common nowadays) rigid standards for configuring access to the Kafka Cluster which often make life difficult for CDC.
* Separate operations team for z/OS systems and LUW systems and Cloud systems.
* Multiple groups within an enterprise that have interests and constraints relative to a heterogenous data replication solution ( Network, Security, Architecture etc...)
* A regular flow of database structure changes on the data sources, that must be included within the subscriptions and reflected in the data targets.

Some of these real-world complexities affect the initial setup project, and cease to be an ongoing concern.
Others affect the ongoing operations of a data replication environment.


