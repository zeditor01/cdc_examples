# Create, Scale and Sustain CDC data replication systems

## Abstract
This github repository is dedicated to addressing the practical aspects of implementing CDC data replication solutions, primarily 
through the use of documenting worked examples of how to build CDC solutions that are easy to operate, manage and maintain.

## The true nature of a heterogenous data replication project
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

## Risk Mitigation
It is very easy to get engrossed in all the technical challenges. Heterogenous data replication is a geek's paradise of technical challenges, and 
you can go down some very deep rabbit holes if you address all the challenges head on without an overarching strategy.

Put your project manager hat on, and review the (probably incomplete) list of technical challenges in the previous section. 
Ask yourself how many different people from different teams need to be involved to make this heterogeneous data replication project work. 
As a side exercise, you might also consider how many different outsourced companies these people work for.
The answers to these questions are the best indication of how difficult this project might be.

It is not the CDC product that makes the creation and sustainability of a heterogeneous data replication hard to manage. 
It is the co-ordination of the multiple teams that are necessarily involved that makes it hard!

Once you have clocked the true nature of the project that you are about to embark upon, you can start to evaluate risk mitigation strategies. 
Aside from Project Management best practices (sponsor, funding, project manager, technical owner, project planning etc...) 
the following high-level strategies (some obvious, other less so) may form part of your risk mitigation aproach

* Have a clear vision of the desired end-point (functional and non-functional requirements, service levels for ongoing devops control) and an honest assessment of the ongoing devops requirements
* Produce a detailed design of the CDC solution, with a focus on the cross-team integration points, and the operational teams that will be responsible for meeting the required service levels.
* Assess the practical ability of the operational teams to work together to meet the required service levels.
* Consider Shift-Left or Shift-Right deployment approaches to reduce the number of different teams with devops involvement
* Identify all the teams that need to contribute to the solution, and specify the initial and ongoing responsibilities of those teams
* Present the solution to the teams, and secure agreement to them supporting their responsibilities (or identify and resolve differing viewpoints)
* Refine the design if appropriate, and proceed with the managed project 



