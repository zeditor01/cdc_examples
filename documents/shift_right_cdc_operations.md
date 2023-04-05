# Shift-Right CDC Operations

This section addresses the practice of minimising or eliminating CDC operational responsibility from the source platform (typically z/OS) 
and establishing all the CDC operational responsibilities on the target side (typically Linux, Unix or Windows)

## Contents

<ul class="toc_list">
<li><a href="#1.0">1. Shift-Right Concepts</a>   
<li><a href="#2.0">2. Shift-Right Operations</a>   
<li><a href="#3.0">3. IBM Remote Capture products
<ul>  
  <li><a href="#3.1">3.1 Remote Capture for Db2 z/OS</a></li>
  <li><a href="#3.2">3.2 Remote Capture for VSAM</a></li>
  </ul>  
<li><a href="#4.0">4. Summary and Recommendations</a> 
</ul>

## 1. Shift-Reight Concepts

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



## 2. Shift-Right Operations
Separating a CDC Capture agent from a source database can be hard because the CDC capture agent will use log APIs 
supported by the DBMS vendor to access the database logs for change data capture, and normally those log APIs are
only available on the same OS as the source database.

Some RDBMS's do enable the database recovery logs to be read from a remote system. However, 
if the source RDBMS is DB2 for z/OS (using EBCDIC code pages) and the target is a Linux RDBMS (using ascii code pages) then 
that's not going to work.

Another approach is to provide a light-touch log reader on the source database (such as a database stored procedure) and call it with an 
SQL connections from a remote platform.

![shift_r](/images/shift_r.png)

Whatever the precise mechanism, the driving force is normally the desire to have all CDC operations and automation managed on a common operating system, 
so that a single support team can take responsibility for the CDC service levels without depending on favours from an independent operations support 
team for a different OS platform.

## 3. IBM Remote Capture products

In support of Shift-Right deployments, IBM has announced two remote capture products for IBM Z data sources. ( Db2 z/OS and VSAM ).

### 3.1 Remote Capture for Db2 z/OS

In the case fo Db2 z/OS, IBM offers a remote capture capability from a linux client. 
The diagram below illustrates the architecture of this product.

![rcapdb2](/images/rcapdb2.png)

In this CDC agent, the Db2 z/OS log is read by a Db2 stored procedure.
The CHCRLRSP stored procedure uses exactly the same Db2 log interface (DB2IFI) as the z/OS started task CDC agent to read the Db2 log.
The difference in the remote capture agent is that the logic to consume the log records and publish changes to downstream CDC apply agents 
is performed on a remote linux server. The capture agent simply calls the CHCRLRSP stored procedure when it wants to read more Db2 log records.

IBM sales presentations sometimes promote the benefit of remote capture agenst as saving mainframe CPU cycles, and executing the stored procedure against zIIP engines. 
Whilst there is some truth in these claims, they are over-rated and not a significant as the importance of having a single operations control plane.

Regarding mainframe cpu savings: The biggest element of the cost of capturing changes is the log read API, which is 
not zIIP eligible even if it is called from a DRDA-called stored procedure. If your reason for using the remote capture agent for Db2 z/OS is 
to save CPU cycles, a realistic expectation of savings would be 40% - 50% of general purpose CPU compared to the local CDC started task. 
And realising that saving will require you to establish a linux server ( with HA backup etc... ) to run the Capture Server.

It is the author's opinion that the primary decision criteria should be where you want to manage CDC operations from. 
If your skilled CDC operations team is linux-based, then the remote capture agent will be ideal, and you will have the added 
benefit of some mainframe CPU efficiencies. 

A worked example of implementing the remote CDC capture agent for Db2 z/OS is available 
at this [link](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_db2zos.md) 


### 3.2 Remote Capture for VSAM

IBM also offers a remote capture capability for VSAM.

The concepts of the remote capture agent for VSAM are conceptually similar to the remote capture agent for Db2 z/OS
* There is a lightweight agent on z/OS that performs the actual log reading
* The remainder of the logic for the capture agent is performed on a linux server.

The remote cdc capture agent for VSAM depends on exactly the same logging implementation as the Classic CDC for VSAM started task option. 
Namely, the VSAM datasets must be augmented, so that they are asscoiated with a z/OS logstream to act as the replication log, and CICS-TS and/or CICS VSAM Recovery must be used to write replication log records.

A worked example of implementing the remote CDC capture agent for VSAM is not yet written, but will be available shortly 
at this [link](https://github.com/zeditor01/cdc_examples/blob/main/documents/deploy_remotecdccapture_vsam.md) 



## 4. Summary and Recommendations

Remote capture agents offer the benefit of establishing a single operational control point within the enterprise on Linux or Windows.

The use of remote capture agents is a great option if your CDC operations support team is linux-based and you 
want to minimise the need to liaise with other operational teams to manage the CDC service level that the business demands.

