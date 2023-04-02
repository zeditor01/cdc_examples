# Create, Scale and Sustain CDC data replication systems

The diagram below is representative of a typical deployment of Infosphere CDC in an enterprise 
with transactional databases on IBM Z. Data from sources such as IMS, Db2 and VSAM may be streamed
in near realtime to various targets for data science, analytics or integration use cases.

![typical_cdc](/images/typical_cdc.JPG)

## Abstract
This github repository is dedicated to addressing the practical aspects of implementing sustainable CDC data replication solutions, primarily 
through the use of documenting worked examples of how to build CDC solutions that are easy to operate, manage and maintain.

This document is a reflection of the author's experiences in deploying CDC solutions at large enterprise clients, and contains 
many practical observations and recommendations that are matters of opinion. 
It is intended to be read in conjuction with 
the [official product documentation](https://www.ibm.com/docs/en/idr/11.4.0?topic=change-data-capture-cdc-replication), 
which is the definitive IBM-provided reference point for CDC.

* Author: neale.armstrong@au1.ibm.com
* IBM Z Data and AI Technical Specialist, IBM Australia.
* Last Updated: April 2023.

## Links to Documents in this repository
The content of this repository has been structured into separate documents as follows

1. [Establishing sustainable devops management of the designed CDC solution](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md) (this document).
2. Deploying CDC agents for [IMS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_ims.md) or [DB2 z/OS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_db2zos.md) or [VSAM](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_vsam.md) or [Kafka](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_kafka.md) or [Db2 for Linux](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_cdc_db2linux.md) or [remote VSAM](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_vsam.md) pr [remote Db2 z/OS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_db2zos.md)
3. [Developing CDC Subscriptions](https://github.com/zeditor01/cdc_examples/blob/main/documents/develop_subscriptions.md)
4. [Securing all points of the CDC solution - Authentication, Authorisation, Encryption](https://github.com/zeditor01/cdc_examples/blob/main/documents/securing_cdc.md)
5. [Monitoring and Alerting for CDC subscriptions](https://github.com/zeditor01/cdc_examples/blob/main/documents/monitoring_alerting.md)
6. [Basic Operations Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/operations.md) 
7. [Performance Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/performance.md)
8. [Devops approaches and Change Management](https://github.com/zeditor01/cdc_examples/blob/main/documents/devops_cdc.md)
9. [Shift-Right deployment options](https://github.com/zeditor01/cdc_examples/blob/main/documents/shift_right_cdc_operations.md) to facilitate an LUW operational control plane
10. [Shift-Left deployment options](https://github.com/zeditor01/cdc_examples/blob/main/documents/shift_left_cdc_operations.md) to facilitate a z/OS operational control plane.

## Contents of this document

1. The true nature of a heterogenous data replication project
2. Project Risk Mitigation
3. CDC Implementation (Theory) 
4. CDC Implementation (Practice)
5. Common Constraints that will make things difficult
6. First Implementation in Test
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

## 5. First Implementation in Test

It is sometimes said that ***"the two happiest days of a boater’s life are the day they buy a boat and the day they sell it"***.

I don't know if the boating claim is true, but I can assert that ***"the two most challenging phases of a CDC project are lining up all the ducks to replicate the very first row, and managing structure changes after the project has gone live"***.

Regarding the first implementation in test, my advice is to establish a test environment with as few of the constraints in effect as possible, so that you can gain confidence in the configuration and mechanical operation of the CDC solution before you introduce the necessary layers of complexity that will be required for a secured production environment.

An example of a staged approach to CDC implementation is illustrated in the diagram below. Implementing CDC between the z/OS agents as a first phase can defer managing the complexity of the downstream environment until after the core z/OS CDC services are bedded down and understood.

![staged_dc](/images/staged_cdc.JPG)

Other examples of a staged approach might include
* getting CDC working in the clear before configuring the environment to support network encryption
* getting CDC working with raw Operating System authentication before configuring LDAP for authetication and authorisation

## 6. Ongoing Devops after Successful Production Deployment

Cast your mind back to the diagram representing a real-world perspective of CDC implementation.
Project Management warning flags should be waving vigorously with the number of separate teams that need to be involved in a project of this type.
But, if you zoom into the teams that are involved in ongoing devops, any organisational inefficiencies will incur an ***ongoing*** cost.

![cdc_crux](/images/cdc_crux.JPG)
Operational Visibility and drill-down
Software maintenance co-ordination
coping with Source DDL changes, and incorporating them into CDC subscriptions




## 7. Shift Left or Shift Right for Devops Sustainability



remote capture agents - promoted for the wrong reasons
true value of shift-right deployments
containers to facilitate shift levt deployments 



## 8 Summary


