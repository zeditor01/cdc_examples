[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Deploy Remote Capture for Db2 z/OS

This chapter is a worked example of setting up remote CDC capture for Db2 z/OS.

## Contents

1. The nature of remote CDC capture for Db2 z/OS, and why you might choose it.
2. Installation of Remote CDC Capture for Db2 z/OS
3. Planning for Configuration of Remote CDC Capture for Db2 z/OS
4. Activation and use of Remote CDC Capture for Db2 z/OS


## 1. The nature of remote CDC capture for Db2 z/OS, and why you might choose it.

The remote capture agent for Db2 z/OS is available as an option for clients who wish to capture changes from Db2 z/OS, 
without deploying the CDC started task on z/OS itself. The potential benefits of this options include

1. a reduction in general purpose CPU consumption compared on z/OS, compared to the z/OS started task.
2. placing operational control responsibilities to a linux operations team, instead of a z/OS operations team.

Some observations about the CDC remote capture for Db2 z/OS:
* The administration tools (Management Console and CHCCLP) work with the remote capture agent in exactly the same way as they work wth a z/OS started task for CDC.
* Performance benchmarks for local and remote capture agents are comparable.
* Software licensing metrics are different, so it may be worth getting quotes from IBM for each option.

***The Choice*** of whether to use the z/OS started task capture agent for Db2 z/OS, or the linux remote capture agent will be influenced by all of these factors, 
but ***the best technical descision will be determined by co-locating the operations responsibility for as many CDC agents as possible on the same platform (be it linux or z/OS).***

The design of the remote capture agent for Db2 z/OS is illustrated in the diagram below

![rcapdb2](/images/rcapdb2.png)

* The reading of changes from the Db2 z/OS log is performed by a stored procedure which is deployed to Db2 z/OS.
* The remainder of the CDC capture tasks are executed on a linux server that calls the stored procedure for db2 log reads only.
* Remote CDC Capture for Db2 z/OS interacts with the admin tools and CDC Apply agents in exactly the same way as any other CDC capture agent.



## 2. Installation of Remote CDC Capture for Db2 z/OS

Installation of the software and configuration to work with Db2 z/OS are separate activities.
This section deals with installing the software binaries.

Remote CDC Capture for Db2 z/OS is available for linux on intel or linux on Z. 

You download the software installer to a path on your chosen linux server, and then execute the installer.

I have chosen to use cdcinst1 as the linux userid that owns all cdc programs for my demo environment.
I created a group ( cdcadm1 ) for the user ( cdcinst1 ) and requested to reset the password for cdcinst1 with the following commands.

```
sudo groupadd -g 970 cdcadm1
sudo useradd -u 1070 -g cdcadm1 -m -d /home/cdcinst1 cdcinst1
passwd cdcinst1 
```

Next, create the directories that the installer will install the program to. 

```
/opt/ibm/InfoSphereDataReplication will hold all the cdc agents
/opt/IBM/InfoSphereDataReplication will hold the access server
```

Make cdcinst1 the owner of those directories, with the following commands

```
chown cdcinst1:cdcadm1 /opt/ibm/InfoSphereDataReplication
chown cdcinst1:cdcadm1 /opt/IBM/InfoSphereDataReplication
```

Also, add cdcinst1 to Sudoers for convenience

```
usermod -aG wheel cdcinst1
```


If the JRE is not installed, do so now with the following command

```
sudo yum install unzip libnsl
```

logon as cdcinst1 and switch to the directory where the installer binary was downloaded to.

unset the DISPLAY variable to force a terminal dialog, rather than a GUI

```
unset DISPLAY
```

Invoke the installer with the following command

```
./setup-iidr-11.4.0.4-5618-linux-x86.bin
```

Respond to the installer dialog, choose language and  specify "Install New"


![rdb201](/images/cdc/rdb201.JPG)

choose datastore tyoe Db2 for z/OS, and confirm the option for "Remote Source"

![rdb202](/images/cdc/rdb202.JPG)

accept (or modify) the installation path, and accept the license terms

![rdb203](/images/cdc/rdb203.JPG)

accept (or modify) the instance path, and review the pre-installation summary.

![rdb204](/images/cdc/rdb204.JPG)

press enter, and watch the installer complete. Then confim that you wish to launch the configuration tool to create an instance.

![rdb205](/images/cdc/rdb205.JPG)

The configuration tool will ask several questions about the instance to be created.
* If the instance was being created in Linux, the questions would continue in the command line prompt style.
* If the instance was being created in Windows, the dialog would be a GUI as shown below

![rcdcdb2win](/images/rcdcdb2win.png)

The dialog asks for the following configuration parameters, all of which are discussed in the planning section below
* Name of instance and listening port
* Space allocations for CDC instance (staging disk and memory)
* Connection details for Db2 z/OS server ( DRDA location name, hostname, port )
* Authentication credentials
* Schema Name for the CDC control objects
* SSH port
* APF-authorized library
* WLM environment name



## 3. Planning for Configuration of Remote CDC Capture for Db2 z/OS

You can't proceed to use the remote CDC capture agent until you have made provision for deploying the stored procedure 
that will read the Db2 logs. Db2 z/OS stored procedures run in WLM-managed address spaces. You need to configure the back end 
environment so that the CDC remote capture agent can deploy the stored procedure and the underlying load module the first time it runs.


The CDC Remote Capture for Db2 z/OS runs on Linux, but requires a load module to be deployed to Db2 z/OS as the executable program  of a stored procedure for access to the asynchronous Db2 log reader. The installation of this stored procedure is performed by the Remote Capture agent when you first start it. In order for this to happen, you need to ensure that a number of criteria are satisfied so that the Remote Capture agent can deploy the stored procedure. 

The pre-requisite criteria for the Remote Capture server to complete the setup are:

***Db2 for z/OS*** must be at Version 11 or later

***Operational DDF Address Space*** Db2 z/OS V11 or V12 must be configured with the DDF address space to support DRDA connectivity (either encrypted or not) from the CDC Remote Capture instance. The vast majority of Db2 z/OS systems in the world are configured to support DRDA connectivity already, so this is probably already in place.

***WLM Environment*** A WLM environment is required for the stored procedure to execute. It should be dedicated to a single instance of the remote CDC Capture agent. The WLM environment must be VARIED on before the Remote Capture product starts. 

The CDC Remote Capture procedure, should generally be managed to use a service class with the same performance goals that are defined for the Db2 z/OS DBM1 address space in your site.

A simple approach might be
1.	For the WLM environment definition, use a copy of the IBM-supplied WLM environment DSNWLM_NUMTCB1
2.	For the workload service class, define the workload for the CDC Capture based on the stored procedure name (CHCRLRSP) used to invoked the log reader.

```
Appl Environment Name : assign something meaningful (eg WLMCDC1)            
Description . . . . . : remote CDC log reader procedure
Subsystem type  . . . : DB2                   
Procedure name  . . . : name of your JCL Procedure used to start the WLM-managed address space              
Start parameters. . . : DB2SSN=&IWMSSNM,APPLENV=’WLMCDC1’
```

***z/OS SSH Server*** An SSH server must be configured in z/OS to allow the Load Module to be transferred to an APF-Authorised Load Library on z/OS.

***APF-Authorised Load Library*** An APF-Authorised Load Library is required to store the load module. This library must be in the STEPLIB of the WLM address space JCL. The actual load module will up uploaded via SSH to this APF-authorised library by the remote capture program when it first connects to Db2 z/OS. This is an unusual method of installing load modules to z/OS, but it's how the remote capture agent for Db2 z/OS works.

***TSO userid*** A TSO userid is required to execute z/OS commands and access Db2 z/OS. It does not need ISPF access. This userid is the schema for the Remote CDC Capture agent in Db2 z/OS. For operational simplicity it would be easiest if the TSO userid had a non-expiring password.

Required TSO userid privileges are
1.	Ability to connect to Db2 z/OS via DRDA
2.	Execute privilege on the APF-Authorised Load Module
3.	SELECT privilege is required on the following Db2 for z/OS catalog tables:

* SYSIBM.SYSCOLUMNS
* SYSIBM.SYSDATATYPES
* SYSIBM.SYSDUMMY1
* SYSIBM.SYSROUTINES
* SYSIBM.SYSPACKSTMT
* SYSIBM.SYSTABLES
* SYSIBM.SYSTABLESPACE

Once you have resolved all these pre-requisites, you are ready to run the instance for the first time, and the stored procedure should be deployed on the 
first execution of the instance.

## 4. Activation and use of Remote CDC Capture for Db2 z/OS

The next step is to define a subscription and start it.
* If the configuration was good, the first subscription should start replicating.
* If there were any configuration problems, the execution logs should highlight the cause of the problem





Done !
