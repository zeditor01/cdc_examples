[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Deploy CHCCLP for z/OS

***Note*** the content of this paper is fairly comprehensive. 
However, it will be tided up shortly to create a better structure with less repetition.

## 1. What is CHCCLP for z/OS ?

The Infosphere CDC product documentation barely mentions CHCCLP for z/OS.
The focus of the administration and operations tooling is the Windows-based Management Console, and the Access Server.
Collectively these tools cater for the entire range of administration and operations tasks that are needed to manage CDC subscriptions.

* Choice of Graphical User Interface or Command/Script interface
* Add Data Source
* Create Subscription
* Start Subscription
* Monitor Subscription
* Stop Subscription
* etc... etc...

Any administration or operation task can be done via either the GUI or by CHCCLP scripts.
CHCCLP scripts can be created with parameterised variables.
CHCCLP scripts are supported
1. at the management console
2. at the access server
3. on z/OS

***CHCCLP for z/OS*** is the CHCCLP script package, deployed to z/OS, accessible via USS command line or JCL.

## Why would you use CHCCLP for z/OS ?

Most CDC sites use a mixture of the Management Console GUI, and CHCCLP scripting.

***The Management Console*** is a very good graphical user interface indeed. 
It's tabbed interface leads you through the entire devops lifecycle of CDC.
It's monitoring interface allows to choose from any of the CDC monitoring counters and create realtime charts to monitor subscriptions.
It's alerting functions allow you to specify operations criteria and thresholds that are worthy of raising alerts.

But there are times when a Windows GUI, however good, do not meet the devops needs of CDC users. For example
* When a site wants system automation that does not depend on manual interventions through a Windows PC
* When tasks like promoting subscriptions from test to production should not be exposed to human error using an interactive GUI.

***CHCCLP*** is a scripting environment that can be used to script any task that can be performed by a user of the GUI. 
The Management Console GUI can generate CHCCLP scripts from a CDC deployment.

CHCCLP is available either from the Management Console, or from the Access Server. It is also available on z/OS.
CHCCLP for z/OS connects directly to the CDC capture and apply agents without a separate access server.
CHCCLP for z/OS allows scripts to be developed and executed om z/OS, and is very useful for sites that want to perfom automation of CDC tasks from a z/OS control plane.


## 2. Devops Considerations for CDC Replication
DevOps is a set of practices that combines software development (Dev) and IT operations (Ops). It aims to shorten the systems development life cycle and provide continuous delivery with high software quality. 
The "text book" way of using CDC Replication is via the Management Console and the Access Server. This is very productive for familiarisation and development and testing. However, good devops practices would tend to avoid fat-client interactive tools to perform operational tasks. 

The disadvantages of using manual, interactive user interfaces for managing CDC Subscriptions in production would include
* Lack of automation potential
* Exposure to typographical errors during manual operation
* Lack of known, predictable outcomes to manual inputs
* Un-necessary moving parts in the deployment pattern that would need TCPIP connections to be TLS-secured and LDAP-authenticated.

Best Practice for modern Devops standards would follow the following principles
* Automated operations of CDC agents and subscriptions.
* Automated monitoring of CDC subscription health indicators.
* Automated alerting of defined health condition thresholds (eg: latency of replication)
* Automated operational actions in response to as many alert conditions as possible
* Notification for manual intervention in as few situations as possible.

Many of these best practice principles can be achieved with CHCCLP. The author of this document recommends the following approach
* Use Management Console and Access Server in development environments, to get the productivity benefits of these tools to build and validate subscriptions.
* Export CHCCLP scripts for defining subscriptions from the development environment, in order to promote the subscriptions to Test and Production. 
* Write CHCCLP scripts for regular operations and monitoring tasks. (CHCCLP scripts for operations and monitoring are relatively simple).
* Wrap your CHCCLP scripts in a suitable execution packaging (eg: JCL jobs for a z/OS operational environment) for automation purposes.

### 2.1 Scope of Devops Activities for CDC Replications
A simplistic set of Devops activities for deploying CDC replication in production and pre-production environments would include
* Create Subscriptions.
* Start Subscriptions.
* Monitor Subscription Health metrics (latency, throughput etc ).
* Stop Subcsciptions.
* Change Subscription properties.
* Delete Subscriptions.

### 2.2 Devops Workflow using Management Console.
The management console is a very helpful reference point in determining the range of Devops tasks that would need to be achieved using CHCCLP scripts. Lets briefly review the things we do with the Management Console in order to create and operate CDC Replication.

***Manage Connections and Authentication and Authorisation via the Access Server :***

The Access Manager Tab allows datastores to be defined to the Access Server repository based on 
* A TCPIP_Address and a Port
* CDC Access Server userid ( Admin, Bruce, cdcadmin ) 
* Authentication credentials that the Access Server userid uses to access the CDC datastores

![mc01](/images/mc01.png)

***Connect to CDC Data Sources :***

The Configuration Tab allows connections to datastores.

![mc02](/images/mc02.png)

***Define Subscriptions :***

The Configuration Tab allows CDC subscriptions to be defined between CDC source datastores and CDC target datastores.

![mc03](/images/mc03.png)

***Operate Subscriptions :***

The Configuration or the Monitoring Tabs allow CDC Subscriptions to be started for mirroring, started for refresh or stopped.

![mc04](/images/mc04.png)

***Monitor Subscriptions :***

The Monitoring Tab allows subscriptions to be to be monitored in many different ways : numerical counters, graphical charts

![mc05](/images/mc05.png)

***Define Alert Thresholds and Raise Alerts when they are breached :***

The Configuration Tab allows alert threshold and alert actions to be defined. Out of the box notifications are available for email or a java class that you write yourself.

![mc06](/images/mc06.png)


### 2.3 Building CHCCLP scripts to perform the same tasks.
CHCCLP scripts are constructed with grouped sets of commands as follows:
1 One command to define the session type
2 Several commands to define the source and target connections and context
3 One or more commands to perform the desired actions.

It is not helpful that the CHCCLP command language is not documented anywhere to read. If you want to find out the syntax for CHCCLP commands you need to connect to an interactive CHCCLP session and request the syntax of the desired commands with the interactive help function. The screenshot below shows the start of a very long list of CHCCLP commands that you can use.

![script01](/images/script01.png)

And an example of getting syntax help for a common command (connect server) is shown below

![script02](/images/script02.png)

The Management Console is very helpful in giving you a head start in building CHCCLP Scripts. By selecting a CDC subscription in your test environment, you can request a CHCCLP script for the same object to be generated.

![script03](/images/script03.png)

This will yield a CHCCLP script that could have be used to define the subscription. Better still, it surfaces the parameters which would change in test and prod environments as runtime variables.

![script04](/images/script04.png)

The most complex CDC scripts that you will need to build are those to define new subscriptions. They could be quite verbose, particularly if your subscription contains many tables with many columns. But these scripts are generated for you.
The remaining scripts needed for the standard range of Devops processes are simply by comparison. You can strip out the context commands (connect to datastore, set context etc) from the CHCCLP script that you just generated, and add the additional simple commands needed to perform a Devops task.
Some examples of CHCCLP scripts are included in section 4 of this paper.
Once generated, the CHCCLP scripts can be submitted, with values for parameter substitutions, for promoting CDC objects to higher level environments. For example, from the CHCCLP command windows provided by Management Console and Access Server the following shell command would process "myfile.chcclp" using the parameter values on the command.
chcclp -f /u/ibmuser/myfile.chcclp uid1:IBMUSER pwd1:SYS1 uid2:targetid pwd2:targetpwd
The same can be done on z/OS, which we will cover in the next section.

### 2.4 Choosing the right Platform for CHCCLP with heterogenous environments.

CHCCLP is available in 3 places
* Within the Windows Management Console installation package
* Within the Access Server installation Package
* Natively for z/OS (without the need for a separate Access Server ! )

A big challenge is where to perform CDC operations from. 
There are a great many heterogenous scenarios where CDC is used to capture changes from mainframe sources ( IMS, Db2, VSAM ) and replicate them to Kafka on intel or the cloud. Should the two operations team share operational responsibility for CDC , or should one team take the lead ?  
Assertion: The best platform to run CHCCLP scripts is (a) where the majority of your CDC estate runs and (b) wherever you have invested in good operational processes and skills.

#### 2.4.1 A Predominantly Midrange Scenario
An enterprise with a dozen CDC datastores on Linux and Windows, and a single z/OS based CDC datastore would probably be best to establish CDC operations on midrange platforms and use the CDC tools on midrange platforms to operate the subscriptions from the mainframe.
Indeed, such an organisation would probably be best served with the remote capture agents for VSAM and Db2 z/OS.

#### 2.4.2 A Predominantly z/OS Scenario
Many Banks will tend to have much stronger operations and automation credentials on the z/OS platform. In such organisations it probably makes most sense to operate the CDC subscriptions from the  z/OS platform. The rest of this paper is focussed on using CHCCLP for z/OS to enable z/OS-based operations to take responsibility for operating the heterogeneous landscape.


## 3. CHCCLP for z/OS
If you have license entitlement to IBM Data Replication, then you can download CHCCLP for z/OS from IBM fix central.


### 3.1 CHCCLP for z/OS Download and Setup 
Go to IBM fix central and search for Classic CHCCLP.

![shopz01](/images/shopz01.png)

Follow these instructions to install CHCCLP for z/OS 
https://www.ibm.com/docs/en/idrfvfz/11.3.0?topic=vsam-setting-up-chcclp-command-line-utility

Download the package and unzip it to find a bunch of jar files.
FTP these files ( in binary ) to a USS directory of your choice. ( /usr/lpp/chcclp in this example )
Make them all executable
chmod 755 *.jar



### 3.2 CHCCLP scripting from USS shell
CHCCLP can be invoked directly from a USS shell as follows
Set the PATH, CLASSPATH, JAVA_HOME and LIBPATH as needed. For example

```
PATH=/bin:/usr/bin                                            
export PATH                                                   
CLASSPATH=/usr/lpp/chcclp/chcclp.jar                          
export CLASSPATH                                              
CLASSPATH=$CLASSPATH:/usr/lpp/java/J8.0_64/bin:/usr/lpp/chcclp
export CLASSPATH                                              
JAVA_HOME=/usr/lpp/java/J8.0_64                               
export JAVA_HOME                                              
LIBPATH=/lib:/usr/lib:$JAVA_HOME/bin/classic                  
export LIBPATH                                                
```

Now invoke CHCCLP with a commad like this
```
java com.ibm.replication.cdc.scripting.Shell -ap 10111 -f /u/ibmuser/test.chcclp uid1:IBMUSER pwd1:SYS1 uid2:targetuser pwd2:targetpwd
```

Where
* com.ibm.replication.cdc.scripting.Shell is the class name for CHCCLP for z/OS
* 10111 is the port for the embedded Access Server !!!
* /u/ibmuser/test.chcclp is the CHCCLP script that you want to execute
* uid1:IBMUSER pwd1:SYS1 uid2:targetuser pwd2:targetpwd are the parameter values

The beauty of CHCCLP for z/OS is that you do not need to deploy a Management Console or Access Server anywhere in your production environment. It contains an embedded access server within the java class which can connect directly to CDC Capture and Apply agents on z/OS and non-z/OS platforms. This means that you can
1. Avoid setting up an LDAP server for Access Server Authentication
2. Avoid the hassle of implementing TLS encryption on all the connections between Management Console, Access Server and Datastored. (CHCCLP for z/OS will take advantage of the AT-TLS policies that you setup for Classic CDC for IMS replication to CDC for Kafka)
3. Deploy all CDC Devops tasks as USS scripts or JCL jobs



### 3.3 CHCCLP scripting from JCL
CHCCLP can also be invoked from JCL members, by using the java batch scheduler component of z/OS. The instructions are here
https://www.ibm.com/docs/en/idrfvfz/11.3.0?topic=utility-sample-jcl-run-chcclp#iiyv2vchcclpsample
This is an example of a JCL job to run a CHCCLP monitoring query.

![jcl01](/images/jcl01.png)

To explain the JCL job :
* The STDENV DD card defines the USS environment 
* JVMPRC86 is the name of the Java Batch Scheduler in my z/OS system. (the program name will change as new versions emerge).
* JAVACLS specifies the java class of the CHCCLP processor
* STDIN specifies  three PDS members that represent my CHCCLP script.
* CONN and DISC contain the Connect and Disconnect commands. These are split into different members because they will be re-used many times.
* MONSUB is the specific member for implementing my monitoring commands.

The CONN member 
* connects directly to Classic CDC for IMS, and specifies the context as CDC Source.
* Connects directly to CDC for Kafka, and specifies the context as CDC Target.

![jcl02](/images/jcl02.png)

The MONSUB member
* Identifies the subscription that it is to operate on
* Requests a number of performance metrics ( records processed etc )
* Requests the current latency of the subscription

![jcl03](/images/jcl03.png)

The DISC member just disconnects from each of the datastores and exits CHCCLP.

![jcl04](/images/jcl04.png)

The Output of this CHCCLP job looks like this

![jcl05](/images/jcl05.png)



## 4. Devops Script Groups
Once you get over the syntax of CHCCLP, actually it can be incredibly simple to use.

### 4.1 Defining Subscriptions
The CHCCLP Syntax for mapping a source table for a subscription is potentially verbose. For example, take a look at CHCCLP script that was generated from Management Console for a subscription from IMS to Kafka.

![syntax01](/images/syntax01.png)

It is not necessary to code "filter cource colum" for every single source column in the IMS tables because
1. If you define your IMS table correctly in Classic Data Architect, you will only surface the fields that you want to replicate.
2. The default values for replicate "yes" and critical "yes" are fine for most use cases, so the filter source column statement are all redundant anyway


In most cases the CHCCLP commands needed to define a subscription would be as simple as the following examples.

![sub01](/images/sub01.png)

Followed by setting the kafka target properties for the subscription


![sub02](/images/sub02.png)

And optionally ( if you want to use a KCOP )


![sub03](/images/sub03.png)

Note : KCOPs ( Kafka Custom Operators ) and java programs that provide formatting and topic-routing capabilities when writing to Kafka targets. As such, they tend to include class names that are longer than 80 characters, so you probably want to define your PDS for CHCCLP scripts to have a large record length.

### 4.2 Operating Replication
CHCCLP scripts to operate replication would be topped and tailed by the standard CONN and DISC scripts, and include commands like this


![op01](/images/op01.png)

And

![op02](/images/op02.png)

### 4.3 Monitoring Replication
When you think about it, monitoring replication should be a fairly straightforward probe because if the latency is below your threshold of , say 1 minute , then by implication everything involved in the replication process must be working ok !
A couple of simple scripts to measure latency and throughput is probably all you need to automate for health verification

![mon01](/images/mon01.png)

and

![mon02](/images/mon02.png)

Resulting in output such as

![mon03](/images/mon03.png)

In most cases you probably want to test for alert thresholds and issue a WTO command if there is a problem, address in section 4.4 below.
If you want to produce a throughput and latency dashboard, then the easiest way would be to write the output of these monitoring commands to a dashboarding tool like Kibana or Splunk, and construct a browser-accessible dashboard that can be displayed from anywhere.



### 4.4 Options for Alerting 
Operations Alerts is the area where you probably need to code a bit of invention to achieve clean Devops processes. 
The paradigm of the CDC Management Console was to define Alert Thresholds of concern and send an email or call a java class when they are breached. The CHCCLP tool allows these thresholds to be defined within an Access Server. But if we do not want to deploy an access server then we need to devise other ways of detecting health conditions and raising alerts.

There are MTO commands to Classic CDC for IMS to get the status of subscriptions, but they don't include the important metrics from the target end.
So realistically the best option is to execute CHCCLP for z/OS scripts to monitor replication throughput and latency, and parse the outputs (below) of these commands in some automated operations control environment.

![mon04](/images/mon04.png)

There are several ways to approach this, for example
* Write a REXX shell script to invoke the CHCCLP monitoring script, parse the output and if there is a health problem call a program to perform a write to the master console for system automation to pick up.
* Schedule JCL jobs to invoke the CHCCLP monitoring script, parse the output etc 





