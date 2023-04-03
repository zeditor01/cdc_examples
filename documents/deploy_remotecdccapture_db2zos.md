# Deploy Remote Capture for Db2 z/OS

1. IBM understanding of Qantas' intended data replication implementation.

Qantas require a low latency stream of Db2 z/OS data changes to be captured from their Db2 z/OS systems, and written to a range of topics within a Kafka cluster that is hosted on a cloud service.

Currently IBM is not aware of the intended number of tables, base data volumes, change data volumes etc...

2. IBM understanding of Qantas' intended Proof of Concept test environment.

For the PoC, IBM understands that Qantas wish to
	use one of their own on-premise Db2 z/OS test systems as the Db2 z/OS source.
	use a Kafka cluster hosted on a cloud service.
	deploy the CDC Capture, CDC Apply, and CDC Access Server components on a linux server that has TCPIP connectivity to both source and target.
	deploy the CDC Management Console to a Windows system

The diagram below represents the PoC environment that IBM anticipates Qantas will deploy. There is some flexibility on the deployment of the CDC components should that be required.

![rcapdb2](/images/rcapdb2.png)

3. Installation and Configuration overview for the PoC.

Four Separate installation activities are required for the PoC environment.

3.1 Install the CDC agent for remote capture from Db2 z/OS on the Linux server. 
The installer is a linux executable that uses a command line dialog to obtain the necessary connectivity information about
a)	CDC agent access to Db2 z/OS
b)	access to the CDC agent from the Access Server.

3.2 Install the CDC agent for Kafka on the Linux server. 
The installer is a linux executable that uses a command line dialog to obtain the necessary connectivity information about
a)	CDC agent access to kafka
b)	access to the CDC agent from the Access Server.

3.3 Install the CDC Access Console. 
The installer is a linux executable that uses a command line dialog to obtain the necessary connectivity information from the Management Console to the Access Server.

3.4 Install the CDC Management Console. 
This is a Windows setup.exe style installer that deploys the Management Console GUI application to a Windows PC.


4. Detailed instructions for setting up the Remote Capture Agent for Db2 z/OS.

The CDC Remote Capture for Db2 z/OS runs on Linux, but requires a load module to be deployed to Db2 z/OS as the executable program  of a stored procedure for access to the asynchronous Db2 log reader. The installation of this stored procedure is performed by the Remote Capture agent when you first start it. In order for this to happen, you need to ensure that a number of criteria are satisfied so that the Remote Capture agent can deploy the stored procedure. 

4.1 z/OS Environment Pre-Requisites for CDC Remote Capture
The pre-requisite criteria for the Remote Capture server to complete the setup are:


Db2
V11.1 or later

Operational DDF.
Db2 z/OS V11 or V12 must be configured with the DDF address space to support DRDA connectivity (either encrypted or not) from the CDC Remote Capture instance. The vast majority of Db2 z/OS systems in the world are configured to support DRDA connectivity already, so this is probably already in place.

WLM Environment.
A WLM environment is required for the stored procedure to execute. It should be dedicated to a single instance of the remote CDC Capture agent. The WLM environment must be VARIED on before the Remote Capture product starts. 

The CDC Remote Capture procedure, should generally be managed to use a service class with the same performance goals that are defined for the Db2 z/OS DBM1 address space in your site.

I Suggest: 
1.	For the WLM environment definition, use a copy of the IBM-supplied WLM environment DSNWLM_NUMTCB1
2.	For the workload service class, define the workload for the CDC Capture based on the stored procedure name (CHCRLRSP) used to invoked the log reader.

Appl Environment Name : assign something meaningful (eg WLMCDC1)            
Description . . . . . : remote CDC log reader procedure
Subsystem type  . . . : DB2                   
Procedure name  . . . : name of your JCL Procedure used to 
                        start the WLM-managed address space              
Start parameters. . . : DB2SSN=&IWMSSNM,APPLENV=’WLMCDC1’

z/OS SSH Server
An SSH server must be configured in z/OS to allow the Load Module to be transferred to an APF-Authorised Load Library on z/OS.

APF-Authorised Load Library
An APF-Authorised Load Library is required to store the load module. This library must be in the STEPLIB of the WLM address space JCL.

TSO userid
A TSO userid is required to execute z/OS commands and access Db2 z/OS. It does not need TSO interactive or ISPF access. This userid is the schema for the Remote CDC Capture agent in Db2 z/OS. For operational simplicity it would be easiest if the TSO userid had a non-expiring password.

TSO userid priveleges
1.	Ability to connect to Db2 z/OS via DRDA
2.	Execute privilege on the APF-Authorised Load Module
3.	SELECT privilege is required on the following Db2 for z/OS catalog tables:
	SYSIBM.SYSCOLUMNS
	SYSIBM.SYSDATATYPES
	SYSIBM.SYSDUMMY1
	SYSIBM.SYSROUTINES
	SYSIBM.SYSPACKSTMT
	SYSIBM.SYSTABLES
	SYSIBM.SYSTABLESPACE


4.2 Installation dialog for the Remote CDC Capture Agent for Db2 z/OS
  The installation dialog (3.1 above) demands the following information to proceed.
	Assign a name for the Remote CDC Capture server.
	Assign a TCPIP port for the Remote CDC Capture server to listen on.
	Define disk storage and memory limits for the Remote CDC Capture agent on Linux
	Define the authentication credentials that the Remote CDC Capture agent uses to execute on the Linux Server
	Define DRDA Connectivity information to the DB2 z/OS source system (TCPIP address, port, DRDA Location Name, userid/password for connection to DB2 z/OS)
	Assign a metadata schema to use for the Remote CDC Capture agent.
	Define SSH connectivity details to allow the transfer the Load Module for the stored procedure. (Host TCPIP address, Host User, Host Schema, SSH Port number).
	Define WLM environment name that the Stored Procedure will execute under.
	Define APF-Authorised Load Library that the Load Module is to be stored in.

4.3 Helpful Screenshots
The installation dialog for the Remote CDC Capture agent on a Windows Platform. 

![rcapdialog](/images/rcapdialog.png)



5.	Notes on Developing Subscriptions and operating them
Once all the CDC components are installed and configured, the process of defining, operating and monitoring subscriptions can commence. 

A subscription can contain mappings for multiple tables to write changes to corresponding Kafka topics. By placing a group of tables in the same subscription, you are ensuring that CDC will apply the changes to that group of tables to Kafka in a transactionally consistent manner.

All of these tasks are typically performed through the Windows-based Management Console, which is designed to provide visually intuitive workflows for productivity.

Additionally, there is a command line option to perform everything that the Management Console is used for. A command line option can be desirable for automation and scripting in a production environment. A common approach is to use the management console in development and test environments to generate scripts that will be used for production deployment.

6.	Notes on Kafka consumers
The change data capture messages written to Kafka are written in an AVRO-JSON format. You can write Kafka consumers using either REST or Java APIs to access the data in Kafka.

IBM provides a number of source code samples of consumers of Kafka topics. These can be used as a template by Qantas to develop their own consumers. The screenshot below is from the IBM sample for a transactionally-consistent consumer of CDC messages.


