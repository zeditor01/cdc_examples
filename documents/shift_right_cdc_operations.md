# Shift-Right CDC Operations

The biggest operational challenge for CDC systems is often the challenges in getting two independent operational 
teams (e.g. z/OS and LUW or cloud ) to form an effective alliance to manage the business service level.

A "conventional" CDC deployment architecture can be represented as follows

![shift_m](/images/shift_m.png)

* The Capture agent executes on the same OS as the source database, and reads the database logs using the interfaces supported by the DBMS in question.
* The Apply agent executes on the same OS as the target database, and establishes a local connection to the target DBMS to write changes to it.
* The capture and apply agents establish a number of TCPIP socket connections to handle communications between each other.

As customer requirements evolved, it became clear that sometimes it was desirable to install the CDC capture or apply agents 
remotely from the database that they were operating against. The list of possible reasons is varied, but includes things like

1. unwillingness to deploy additional software on a server or cluster that is running a mission critical database
2. operational independence between the application (CDC) and the database
3. grouping all CDC operations support under a single platform team ( Windows, Linux, Unix, z/OS ... )
4. minimising distribution of workload from an expensive operting platform to a cheaper operating platform
5. etc...



## Shift-Right Operations
Separating a CDC Capture agent from a source database is harder because the CDC capture agent will use log APIs 
supported by the DBMS vendor to access the database logs for change data capture, and normally those log APIs are
only available on the same OS as the source database.

![shift_r](/images/shift_r.png)


## IBM Remote Capture products

In support of Shift-Right deployments, IBM has announced two remote capture products for IBM Z data sources. ( Db2 z/OS and VSAM ).

### Remote Capture for Db2 z/OS

Link [RCAPDB2ZOS](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_db2zos.md) 

![rcapdb2](/images/rcapdb2.png)

### Remote Capture for VSAM

Link [RCAPVSAM](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_vsam.md) 



## Summary and Recommendations

remote capture agents offer the benefit of establishing a single operational control point within the enterprise on Linux or Windows.

IBM sales presentations sometimes promote the benefit of remote capture agenst as saving mainframe CPU cycles. 
Whilst there is some truth in these claims, they are over-rated and not a significant as the importance of having a single operations control plane.

Picture of a Shift-Right solution.

References to deploying remote capture agents.

Observations about remote capture for Db2 z/OS

Observations about remote capture for VSAM

