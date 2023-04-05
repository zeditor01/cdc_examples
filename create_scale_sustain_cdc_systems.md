# Create, Scale and Sustain CDC data replication systems

The diagram below is representative of a typical deployment of Infosphere CDC in an enterprise 
with transactional databases on IBM Z. Data from sources such as IMS, Db2 and VSAM may be streamed
in near realtime to various targets (Kafka, LUW RDBMS's) for use cases such as data science, analytics or data integration.

![typical_cdc](/images/typical_cdc.JPG)

## Abstract
This github repository is dedicated to addressing the practical aspects of implementing sustainable CDC data replication solutions, primarily 
through the use of documenting worked examples of how to build CDC solutions that are easy to operate, manage and maintain.

This document is a reflection of the author's experiences in deploying CDC solutions at large enterprise clients, and contains 
many practical observations and recommendations. 
It is intended to be read in conjuction with 
the [official product documentation](https://www.ibm.com/docs/en/idr/11.4.0?topic=change-data-capture-cdc-replication), 
which is the definitive IBM-provided reference point of information for CDC Software Solutions.

* Author: neale.armstrong@au1.ibm.com
* IBM Z Data and AI Technical Specialist, IBM Australia.
* Last Updated: April 2023.

## Links to Documents in this repository
The content of this repository has been structured into separate documents as follows

1. [Establishing sustainable devops management of the designed CDC solution](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md) (this document).
2. Deploying selected CDC agents... 
    * [Classic CDC for IMS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_ims.md)
    * [CDC for DB2 z/OS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_db2zos.md)
    * [Classic CDC for VSAM](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_vsam.md)
    * [CDC for Kafka](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_kafka.md)
    * [CDC for Db2 for Linux](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_db2linux.md)
    * [remote capture for VSAM](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_vsam.md)
    * [remote capture for Db2 z/OS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_db2zos.md)
3. Deploying [Management Console and Access Server](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_admintools.md)
4. Deploying [CHCCLP for z/OS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_chcclp_zos.md)
5. [Developing CDC Subscriptions](https://github.com/zeditor01/cdc_examples/blob/main/documents/develop_subscriptions.md)
6. [Securing all points of the CDC solution - Authentication, Authorisation, Encryption](https://github.com/zeditor01/cdc_examples/blob/main/documents/securing_cdc.md)
7. [Monitoring and Alerting for CDC subscriptions](https://github.com/zeditor01/cdc_examples/blob/main/documents/monitoring_alerting.md)
8. [Basic Operations Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/operations.md) 
9. [Performance Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/performance.md)
10. [Devops approaches and Change Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/devops_cdc.md)
11. [Shift-Right deployment options](https://github.com/zeditor01/cdc_examples/blob/main/documents/shift_right_cdc_operations.md) to facilitate an LUW operational control plane
12. [Shift-Left deployment options](https://github.com/zeditor01/cdc_examples/blob/main/documents/shift_left_cdc_operations.md) to facilitate a z/OS operational control plane.

## Contents of *this* document

1. The true nature of a heterogenous data replication project
2. Project Risk Mitigation
3. CDC Implementation (Theory) 
4. CDC Implementation (Practice)
5. Common Constraints that will make things difficult
6. Staggered Deployment Steps
7. Ongoing Devops after Successful Production Deployment
8. Shift Left or Shift Right for Devops Sustainability

## 1. The true nature of a heterogenous data replication project
Establishing CDC replication solutions in a "simple unconstrained environment" is fairly straightforward. 
* Install and configure the CDC components (capture agent, apply agent, admin tools)
* Define data replication subscriptions
* Start and monitor those subscriptions 

Simple huh ?

***No!***

Enterprise customers do ***not*** have "simple unconstrained environments". 
The production environments where CDC must be deployed are highly evolved to provide well-secured, high-performance, 
continuously-available services for highly visible transactional systems that cannot tolerate service outages.
It is the critical nature of these production systems that is often the driving force for establishing data replicas on 
other systems for purposes such as analytics, data science, digital integration and so on. But the very practices that 
are established to protect the demanding service levels of these business-critical systems are the same practices that make the deployment of 
heterogeneous CDC replication solutions challenging.

The list below are just of the complexities that a typical CDC deployment will face is a real-world environment.
* Strict governance controls for database configuration changes
* Complex network paths to navigate (firewalls, jump boxes etc...).
* Requirement for network encryption for CDC data flows.
* Conformance to cross-platform authentication and authorisation standards (LDAP etc...)
* Understanding the differences between application-transparent TLS (z/OS) and application-controlled TLS (most other systems)
* different specialist groups to manage typical data sources ( IMS, Db2 z/OS, VSAM ) and typical data targets ( Kafka, SQL Server, Oracle )
* For Kafka targets (very common nowadays) rigid standards for configuring access to the Kafka Cluster which often make life difficult for CDC.
* Separate operations team for z/OS systems and LUW systems and Cloud systems.
* Multiple groups within an enterprise that have interest in and influence on a heterogenous data replication solution ( Network, Security, Architecture etc...)
* Devops practices to support a regular flow of database structure changes on the data sources, that must be included within the subscriptions and reflected in the data targets.
* etc... etc...

Some of these real-world complexities affect the initial setup project, and cease to be an ongoing concern.
Others affect the ongoing operations of a data replication environment. 

## 2. Project Risk Mitigation
It is very easy to get engrossed in all the technical challenges. Heterogenous data replication is a geek's paradise of technical challenges, and 
you can go down some very deep rabbit holes if you address all the challenges head on without an overarching strategy.

Put your project manager hat on, and review the (probably incomplete) list of technical challenges in the previous section. 
Ask yourself how many different people from different teams need to be involved to make this heterogeneous data replication project work. 
As a side exercise, you might also consider how many different outsourced companies these people work for.
The answers to these questions are the best indication of how difficult this project might be.

***It is not the CDC product that makes the creation and sustainability of a heterogeneous data replication hard to manage. 
It is the co-ordination of the multiple teams and technologies that are necessarily involved that makes it hard!***

Once you have clocked the true nature of the project that you are about to embark upon, you can start to evaluate risk mitigation strategies. 
Aside from normal Project Management best practices (sponsor, funding, project manager, technical owner, project planning etc...) 
the following high-level strategies (some obvious, other less so) may form part of your risk mitigation aproach

* Have a clear vision of the desired end-point (functional and non-functional requirements, service levels for ongoing devops control) and an honest assessment of the ongoing devops requirements
* Produce a detailed design of the CDC solution, with a focus on the cross-team integration points, and the operational teams that will be responsible for meeting the required service levels on an ongoing basis.
* Assess the practical ability of the operational teams to work together to meet the required service levels.
* Consider Shift-Left or Shift-Right deployment approaches to reduce the number of different teams with devops involvement
* Identify all the teams that need to contribute to the solution, and specify the initial and ongoing responsibilities of those teams
* Present the solution to the teams, and secure agreement to them supporting their responsibilities (or identify and resolve differing viewpoints)
* Refine the design if appropriate, and proceed with the managed project 

## 2. CDC Implementation (Theory)

CDC solutions, in theory, have a complex set of moving parts to co-ordinate, but are easy to manage with the centralised control plane. 
A simple architectecture diagram might look like this.

![cdc_in_theory](/images/cdc_in_theory.JPG)

## 3. CDC Implementation (Real World)

CDC in practice is rather more complicated to manage. The causes of the complexity lie the heterogeneous nature of the typical CDC solution.
And whilst all the technical integration points use standards-based patterns, it is the number of different teams that need to be co-ordinated
that will be the most demanding aspect of project management for a heterogeneous data replication project. 
Review the representations of the different teams (the green boxes) and map this example to your own organisational structures.

![cdc_in_practice](/images/cdc_in_practice.JPG)

## 4. Common Constraints that will make things difficult

Each deployment will enounter it's own unique set of challenges, based on the organisation, technology and technical standards applicable at that site.
The list below is a compilation of some the the challenges that occur most frequently across sites.

* for IMS, implications of increased logging and implementation of IMS exits
* for IMS, augmentation of DBDs to generate log record types needed for replication
* for VSAM (or IAM), implementation of z/OS logstreams to serve replication
* for Db2 z/OS, DDL break in difficulties (completely solved by recent Db2 V13 PTF)
* for Kafka, configuring CDC (a Kafka producer and consumer) to integrate with site-specific Kafka standards
* Schema management (whether RDBMS or Kafka)
* Choice of using a Windows client tool for production operations, or automation with scripting tools
* Change management procedures that span the breadth of the heterogeneous data replication solution
* Operations control

Each of these considerations is addressed directly in the deployment documents linked at the top of this document.

## 5. Staggered Deployment Steps

It is sometimes said that ***"the two happiest days of a boater’s life are the day they buy a boat and the day they sell it"***.

I don't know if the boating claim is true, but I can assert that ***"the two most challenging phases of a CDC project are lining up all the ducks to replicate the very first row, and managing structure changes after the project has gone live"***.

Regarding the first implementation in test, my advice is to establish a test environment with as few of the constraints in effect as possible, so that you can gain confidence in the configuration and experience in the mechanical operation of the CDC solution before you introduce the necessary layers of complexity that will be required for a secured production environment.

An example of a staged approach to CDC implementation is illustrated in the diagram below. Implementing CDC between the z/OS agents as a first phase can defer managing the complexity of the downstream environment until after the core z/OS CDC services are bedded down and understood.

![staged_dc](/images/staged_cdc.JPG)

Other examples of a staged approach will depend on the deployment environment, but might include
* getting CDC working in the clear before configuring the environment to support network encryption
* getting CDC working with raw Operating System authentication before configuring LDAP for authetication and authorisation

## 6. Ongoing Devops after Successful Production Deployment

Cast your mind back to the diagram representing a real-world perspective of CDC implementation.
Project Management warning flags should be waving vigorously with the number of separate teams that need to be involved in a project of this type.
Now, zoom into the teams that are involved in ongoing devops to promote database structure changes though that database and network layers.

![cdc_crux](/images/cdc_crux.JPG)

Any organisational inefficiencies in ongoing devops will incur an ***ongoing*** cost for the life of the replication solution. 

If you have two separate operational teams for the z/OS Capture and the LUW Apply agents, then you will likely have two separate database administration teams, and you are also likely to have two separate change control teams/processes to navigate.

This may not be a big issue if you don't need to reflect changes in the source database structures in the target database structures. Generally speaking most databases changes on the source will be "Add Table" or "Add Column" type of changes. CDC Replication will continue unaffected as changes like these are made to the source database.

However, if you need to reflect changes that occur in the source database, you will need to co-ordinate them with changes to the target database, and changes to thhe CDC replication mapping metadata, and promotion from test to production. IIDR (IBM Infopsphere Data Replication) does support the concept of inline "DDL Replication" without operational interruption when replicating between some homogeneous databases. DB2 z/OS, Oracle and Db2 LUW can all be supported for some capability of inline "DDL Replication". But heterogeneous data replication scenarios do not support "DDL Replication", and devops procedures must be established and tested to handle changes in database structures that must be reflected in the targets. 

Whatever your operationa management team(s) structure is, you must ensure that it's processes are streamlined to handle database structure changes efficiently. 

## 7. Shift Left or Shift Right for Devops Sustainability

One way of reducing the friction that can arise from multiple different operational teams, is to bring them together into a single team. A decision like this can be made easier by adopting a Shift-Right or a Shift-Left deployment architecture.

### 7.1 Shift-Right Deployment

IBM has produced remote agents for CDC capture from Db2 z/OS and VSAM. This allows the capture and apply agents to both be deployed on linux for intel, and even on the same Linux server. 

![cdc_shiftright](/images/cdc_shiftright.JPG)

There is a relatively small benefit in reducing the z/OS cpu consumption for the capture agent, compared with running the capture agent on z/OS. 
The cpu saving is small because the logs are still read on z/OS (using a stored procedure) and 
the log read (which is the most expensive part of the capture process) is not zIIP elgible. 
But there is a potentially huge operational benefit if you want to build your CDC operational support team and processes on linux on intel.

If you want to establish CDC operational support in a single team, and the skills of that team are based on linux, then you should consider using the CDC remote capture agents for Db2 z/OS and VSAM.

### 7.2 Shift-Left Deployment

IBM supports CDC software whether deployed natievly on an OS, in a virtual machine, or in a software container. In all cases, you are responsible for the correct deployment in the chosen environment, and after that point IBM Support will provide support for your chosen deployment environment.

![cdc_shiftleft](/images/cdc_shiftleft.JPG)

z/OS V2.4 or later supports software containers via a licensed feature called zCX (z/OS container extensions). Put simply, zCX allows docker containers (compiled for the s390 chip architecture) to be deployed inside z/OS. Once deployed as a docker container within zCX it is indistinguishable from a CDC agent deployed on Linux or within a VM images.

If you want to establish CDC operational support in a single team, and the skills of that team are based on z/OS, then you should consider deploying your CDC Apply agents as a software container within zCX.


## 8 Summary and Recommendations

The very nature of heterogeneous data replication solutions means that their operation will span multiple technical and operations support teams.
This, in turn, is the the biggest change for managing a successful CDC deployment project.

Every scenario and enterprise is unique, and the appropriate project management techniques will need to be customised to the specific scanerio.

The best advice I can offer (which needs to be translated into the context of a specific CDC project) is

* ***Eyes Open*** and be mindful of the extent to which the project spans heterogeneous platforms and teams.
* ***Requirements Clarity*** over the business need to incorporate source data structure changes into the downstream replication solution.
* ***Written Definition*** for service levels required by the business for data latency and operational support to resolve problems.
* ***Candour*** in bi-directional dialog with project teams over their expectations for co-operating with other teams
* ***Simplification*** of ongoing operational support team processes 

The documents in this github repository are worked examples of how to implement CDC components in heterogeneous environments 
and configure them to work together. The magic sauce for a successful CDC project is finding a way to streamline the operational 
support processes for efficient devops after the initial deployment.

Neale Armstrong, IBM Australia, April 2023.
neale.armstrong@au1.ibm.com  


