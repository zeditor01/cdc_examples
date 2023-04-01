[Back to README.md and Table of Contents.](README.md)

# Setting Up Classic CDC for IMS - Worked Example
This chapter is a worked example of setting up Classic CDC for IMS. 

## Contents

<ul class="toc_list">
<li><a href="#abstract">Abstract</a>   
<li><a href="#1.0">1 Introduction to Classic CDC for IMS</a>
<ul>
  <li><a href="#1.1">1.1 Requirements to Replicate IMS Data</a></li>
  <li><a href="#1.2">1.2 The Classic CDC Started Task</a></li>
</ul>
<li><a href="#2.0">2. High Level Review of Implementation Steps</a>
<li><a href="#3.0">3. SMPE Installation of Code Libraries</a>
<li><a href="#4.0">4. Creating the Classic CDC Instance</a>
<ul>
  <li><a href="#4.1">4.1 Create the Instance Libraries</a></li>
  <li><a href="#4.2">4.2 Edit the Parameters member</a></li>
  <li><a href="#4.3">4.3 Generate the Customised JCL members</a></li>
  <li><a href="#4.4">4.4 Define CDC Logstreams</a></li>  
  <li><a href="#4.5">4.5 Define and Mount the Classic Catalog zFS</a></li>
  <li><a href="#4.6">4.6 Define and Populate the Configuration Datasets</a></li>
  <li><a href="#4.7">4.7 Create the Metadata Catalog</a></li>
  <li><a href="#4.8">4.8 Create the Replication Mapping Datasets</a></li>  
  <li><a href="#4.9">4.9 Setup Encrypted Passwords</a></li>  
</ul> 
<li><a href="#5.0">5. Configure the z/OS Environment</a>
<ul>
  <li><a href="#5.1">5.1 APF Authorised Load Libraries</a></li>
  <li><a href="#5.2">5.2 TCPIP Ports</a></li>
  <li><a href="#5.3">5.3 RACF Started Task ID</a></li>
  <li><a href="#5.4">5.4 Test Start the Classic CDC Server</a></li>  
</ul>
<li><a href="#6.0">6. Configure the IMS Environment</a>
<ul>
  <li><a href="#6.1">6.1 DRA Startup Module</a></li>
  <li><a href="#6.2">6.2 IMS Logging and Notification Exits</a></li>
  <li><a href="#6.3">6.3 Augment DBD to generate T99 Log Records</a></li>
  <li><a href="#6.4">6.4 Copy the IBM-Supplied Exits into IMS RESLIB</a></li>  
</ul>
<li><a href="#7.0">7. Integrate with the wider CDC Landscape</a>
<ul>
  <li><a href="#7.1">7.1 Deploy Classic CDC as a Started Task</a></li>
  <li><a href="#7.2">7.2 Deploy and use the Classic Data Architect IDE</a></li>
  <li><a href="#7.3">7.3 Connect from Management Console to Classic CDC Started Task</a></li>
  <li><a href="#7.4">7.4 Use CHCCLP Scripting</a></li>  
  <li><a href="#7.5">7.5 Conforming to site standards for cross-platform devops and security</a></li>
</ul> 
</ul>


<br><hr>

<h2 id="abstract"> Abstract</h2>
This document is a basic worked example of setting up Classic CDC for IMS as a CDC Source Server. 

* It deals with the practical considerations for implementing IMS as a CDC data source. 
* It's scope is limited to a "basic up and running guide", and is intended to be easy to follow (assuming a base of z/OS and IMS practical experience).
* It does not attempt to cover all the product's features.
* Comprehensive details of the product's features are covered in <a href="https://www.ibm.com/docs/en/idr/11.4.0?topic=replication-infosphere-classic-cdc-zos">IBM Classic CDC knowledge centre.</a>
 

It is part of a series of documents providing practical worked examples and 
guidance for seting up CDC Replication between mainframe data sources and mid-range or Cloud targets.
The complete set of articles can be accessed using the README link at the very top of this page. 

<br><hr>

<h2 id="1.0">1. Introduction to Classic CDC for IMS</h2>  

Classic CDC for IMS is a CDC Capture Source only. It does not have CDC Apply functionality.

<b>Aside:</b> Classic CDC for IMS is licensed seperately from Classic CDC for VSAM. However, these two 
products share a lot of common components. The sister document [Chapter 10.  Setting up Classic CDC for VSAM.](C010_vsam.md) follows the same pattern as this worked 
example for much of the setup work, but has differences with regard to the services to access VSAM and capture changes from VSAM. 

CDC Replication is a set of products that implement a common data replication architecture spanning 
a large number of diverse data sources and targets. The CDC common architecture is based upon replication of 
data that conforms to the relational model. Any CDC capture or apply agent that supports a non-relational data structure 
must perform whatever conversion work that is necessary to implement a mapping between that data structure and the 
relational model of data. 

<h3 id="1.1">1.1 Requirements to Replicate IMS Data</h3> 

The core functionaility of any CDC Capture agent is to read the source database logs asynchronously, 
stage captured data (preferably in memory) until the Unit of Work is commited or rolled back, 
and then publish the committed changes over TCPIP sockets to a CDC Apply agent.

In addition to the usual requirements, Classic CDC for IMS needs to handle the fact that IMS data is stored 
in hierarchical structures (not relational), and the Log configurations supporting IMS workloads (online and batch) 
are significantly different to a comparatively simple recovery log used by most relational databases.

<b>Regarding hierarchical data structures:</b> The Classic CDC for IMS product provides 
a tool (Classic Data Architect) to map the hierarchical data structures
into relational projections of that data, for the purposes of acting as a CDC Replication capture agent. 
The mappings of data structures are stored in a zFS dataset called the "Classic Catalog". This contains relational catalog tables 
like Db2 ( sysibm.systables , sysibm.syscolumns etc... ) that contain the mapping between the fields in the relevant copybooks for the 
IMS data, and their relational projection. The mapping information allows SQL access to the IMS database to retrieve the base data, 
and also allows IMS log records to be consumed for the purposes of capturing IMS database changes.</p>

<b>Regarding IMS logs:</b> Interfaces must be established between Classic CDC and the IMS logging environment, which is managed by DBRC. 
DBRC provides Classic CDC with information about all the IMS log datasets that it needs to track, and notifies Classic CDC when 
log datasets become full and are switched. 
Additionally, new log record types (Type 99) must be cut for IMS databases, because standard Type 50 log records do not contain enough 
information for the purposes of data repliaction.


<h3 id="1.2">1.2 The Classic CDC Started Task</h3>
The diagram below is a representation of the components within a Classic CDC for IMS started task, and how they 
relate to external artefacts.

![Classic CDC Started Task](../images/cdc/ccdc_services.PNG)

The services that are outlined in red are the ones that can be protected by the SAF Exit. 
The primary services involved in the cdc capture server are  the IMS Log Reader Service (IMSLRS) and the Capture process (Capture). 
The following is a brief summary of what some of the key services do. 

| Service | Function |
| --- | --- |
| Connection Handler | Listens on a TCPIP port for requests from other CDC components (Classic Data Architect, Management Console & Access Server, CDC Apply agents). |
| Admin Service | Dispatches tasks to appropriate services within the Started Task. |
| Query Processor | Converts SQL requests into DL/I language to access IMS data structures. |
| DRA | Service to connect to IMS via the IMS Database Remote Access interface. |
| IMSLRS | IMS Log Reader Service. |
| Capture | Extracts log changes needed for subscriptions, and publishes them over TCPIP to CDC Apply Agents. |
| Operator | Command Processor. (Start, Stop, Park etc... ). |
| Logger | Writes Started Task logs to z/OS logstreams. |
| Monitor | Tracks health and performance data. |

All the services, and their governing parameters are documented in the knowledge 
center <a href="https://www.ibm.com/docs/en/idr/11.4.0?topic=zos-configuration-parameters-classic-data-servers-services">Classic Services</a>.

<br><hr>

<h2 id="2.0">2. High Level Review of Implementation Steps</h2>

There are a lot of moving parts, and a lot of inter-related dependencies in setting up Classic CDC for IMS. 
It is helpful to establish a structured overview of the main installation and configuration activities before 
diving into the technical details of very nut and bolt. 
This paper identifies five separate stages of implementation


1. SMPE Installation of Code Libraries
2. Creating the Customised Classic CDC Instance
3. Configure the z/OS Environment
4. Configure the IMS Environment
5. Integrate with the wider CDC Landscape


<br><hr>

<h2 id="3.0">3. SMPE Installation of Code Libraries</h2>
<p>SMPE installation is a well documented, standardised process that every systems programming shop manages with 
their own standards. It is outside the scope of this paper, aside from noting that it is a pre-requisite to the 
followng stages.</p>
<p>Once the SMPE installation is complete, the following target libraries will exist under the chosen high level qualifier. (CCDC in this example).</p>

 <table>
  <tr><td width=200><b>Library</b></td><td width=500><b>Contents</b></td></tr> 
  <tr><td>CCDC.SCACBASE</td><td>Samples</td></tr> 
  <tr><td>CCDC.SCACCONF</td><td>Configuration datsets</td></tr>  
  <tr><td>CCDC.SCACLOAD</td><td>Load modules</td></tr>  
  <tr><td>CCDC.SCACMAC</td><td>Macros</td></tr>  
  <tr><td>CCDC.SCACMSGS</td><td>Messages</td></tr> 
  <tr><td>CCDC.SCACSAMP</td><td>Sample JCL</td></tr>  
  <tr><td>CCDC.SCACSIDE</td><td>Load Modules</td></tr>  
  <tr><td>CCDC.SCACSKEL</td><td>ISPF skeleton library</td></tr>  
</table> 

<br><hr>
	
<h2 id="4.0">4. Creating the Classic CDC Instance</h2>
<p>The installed product code can now be used to support one or more instances of Classic CDC for IMS. 
This worked example will create a single instance, under the instance high level qualifier of CCDC.I1 (in this example).</p>

<h3 id="4.1">4.1 Create the Instance Libraries</h3> 
<p>Creating the instance libraries is easy. Job CECCUSC1 will allocate the instance library, 
and populate the instance library with a parameters file and a job to generate fully customised versions of all the JCL and configuration members that you need</p>
<p>Just edit CCDC.SCACSAMP(CECCUSC1) to specify the high level qualifier for 
the installation library (CACINHLQ=CCDC) and the instance library (CACUSHLQ=CCDC.I1), and submit the job to create the instance libraries.</p> 

```
//GENSAMPE EXEC GENSAMP                                          
//CRTSAMP.SYSTSIN  DD *                                          
 PROFILE NOPREFIX  MSGID                                         
                                                                 
 ISPSTART CMD(%CACCUSX1                                         +
      CACDUNIT=SYSALLDA                                         +
      CACINHLQ=CCDC                                             +
      CACUSHLQ=CCDC.I1                                          +
      ISPFHLQ=ISP                                               +
      ISPFLANG=ENU                                              +
      SERVERROLE=(CDC_IMS_SRC)                                  +
 )                                                               
/*                                                                                                                             
```


The result should be to allocate the following libraries. 
 <table>
  <tr><td width=200>Library</td><td width=500>Initial Contents</td></tr> 
  <tr><td>CCDC.I1.USERCONF</td><td>empty</td></tr> 
  <tr><td>CCDC.I1.USERSAMP</td><td>just a parameters member plus generation job, listed below:</td></tr>  
</table> 

```
CECCUSC2                                                         
CECCUSPC                                                                                                                   
```


<h3 id="4.2">4.2 Edit the Parameters member</h3> 

Editing the parameters member <code>CCDC.I1.USERSAMP(CECCUSPC)</code> is a critical step. It will generate 
fully customised JCL for pretty much everything you need to do to deploy the Classic CDC instance in your environment. 
You will need to gather configuration information for z/OS, TCPIP, IMS, MQ and so forth to populate the parameters member. 

Many of the default paramaters will be just fine. You should review the descriptions for each of the parameters that are 
provided inside the parameters member to assess whether they need to be changed in your system. 
In this worked example the parameters (and line numbers for V11.3) that we edited were as follows. 

<ul>
<li>Line 43, specify the installation HLQ <code>CACINHLQ="CCDC"</code> 
<li>Line 45, specify the instance HLQ <code>CACUSHLQ="CCDC.I1"</code> 
<li>Line 47, specify the default UNIT for DSN creation <code>CACDUNIT="SYSALLDA"</code> 
<li>Lines 58 and 59, provide a job card for all the JCLs that will be generated
<li>Line 69, provide the HLQ for the various source datasets that will be created for this instance <code>CDCPHLQD="CCDC.I1.CDCSRC"</code>
<li>Line 81, provide the zFS path for the Metadata Catalog that will be created <code>CATPATH="/opt/IBM/ccdci1/catalog"</code> 
<li>Line 103, provide the TCPIP listener port for the Server <code>CDCPPORT="9087"</code>  
<li>Line 104, provide the Data Source Name for the Server <code>CDCDSRCE="SAMPLEDS"</code>  
<li>Line 104, provide the SYSADM ID for the Server <code>CDCADMUS="ADMUSER"</code>  
<li>Line 154, Accept the default supplied security exit name for the SAF-protected services <code>CDCPSAFX="CACSX04"</code> 
<li>Line 185 and 192, Comment the storage classes for the logstreams (which will result in DASD logstreams, rather than CF logstreams <code>**CPLGSC="STG1"</code> and <code>**CPEVSC="STG1"</code> 
<li>Line 198, provide the HLQ for the IMS Libraries <code>IMSINHLQ="DFSF10"</code> 
<li>Line 208, provide the HLQ for the IMS RESLIB <code>IMSSTEPL="&IMS..SDFSRESL"</code> 
<li>Line 228, provide the HLQ for the IMS DBD Library <code>IMSDBDLB="&IMS..DBDLIB"</code> 
<li>Line 232, provide the TCPIP port for the Log Reader Notification Exit <code>IMCPNPRT="5003"</code>
<li>Line 235, provide the Userid that will access IMS via DRA <code>IMSDRAUS="IBMUSER"</code>
<li>Line 236, provide the identification suffix for the DRA Table to use <code>IMSDRASX="00"</code> 
</ul>

... and optionally, if you also want to use the ability to publish changes to MQ (in addition to CDC Apply agents) 

<ul>
<li>Line 249, provide the HLQ for the MQ Libraries <code>CDCMQHLQ="CSQ911"</code>
<li>Line 250, provide the name of the Queue Manager to connect to for MQ publishing<code>CDCMQMGR="CSQ9"</code>
<li>Line 255, provide the name of the Bookmark Queue to use for MQ publishing <code>CDCBKMKQ="CCDC.BOOKMARK"</code> 
<li>Line 260, provide the name of the Subscription Queue to use for MQ publishing <code>CDCSUBLQ="CCDC.IMSPUB"</code>
</ul>

Browse the entire PARMS file in the github repository (opens new tab) <a href="https://github.com/zeditor01/recipes/blob/main/samples/cdc/CECCUSPC_IMS.TXT" target="_blank">here.</a>

<h3 id="4.3">4.3 Generate the Customised JCL members</h3> 

The list of parameters above is enough to completely define the Classic CDC instance. All you need is to run a program 
that will merge those paramaters with some skeleton files in order to generate all the customised JCL and configuration members from them. 
Simply edit and submit <code>CCDC.I1.USERSAMP(CECCUSC2)</code> to do this. 

You should end up with 4 configuration members in <code">CCDC.I1.USERCONF</code> and 43 JCL members in <code>CCDC.I1.USERSAMP</code>. 


<h3 id="4.4">4.4 Define CDC Logstreams</h3> 

Classic CDC uses z/OS Logstreams for the diagnostic log and the event log. The logstreams can be either CF logstreams or DASD logstreams. 
By commenting out the Logstream Storage Class they will ge generated as DASD logstreams in this simple example. 

Review and submit member <code>CCDC.I1.USERSAMP(CECCDSLS)</code> to define these as DASD logstreams. 
```
//*********************************************
//* ALLOCATE THE LOG STREAM FOR THE EVENT LOG *
//*********************************************
//ALLOCEV    EXEC  PGM=IXCMIAPU                
//SYSPRINT DD SYSOUT=*                         
//SYSOUT   DD SYSOUT=*                         
//SYSIN    DD *                                
   DATA TYPE(LOGR) REPORT(NO)                  
   DEFINE LOGSTREAM NAME(CDCSRC.EVENTS)        
          DASDONLY(YES)                        
          MAXBUFSIZE(4096)                     
          LS_SIZE(1024)                        
          STG_SIZE(512)                        
          RETPD(14) AUTODELETE(YES)            
/*                                             
```

and 

```
//************************************************
//* ALLOCATE THE LOG STREAM FOR THE DIAGNOSTIC LOG
//************************************************
//ALLOCDG    EXEC  PGM=IXCMIAPU                   
//SYSPRINT DD SYSOUT=*                            
//SYSOUT   DD SYSOUT=*                            
//SYSIN    DD *                                   
   DATA TYPE(LOGR) REPORT(NO)                     
   DEFINE LOGSTREAM NAME(CDCSRC.DIAGLOG)          
          DASDONLY(YES)                           
          LS_SIZE(1024)                           
          STG_SIZE(512)                           
          RETPD(7) AUTODELETE(YES)                
/*                                                
```

<h3 id="4.5">4.5 Define and Mount the Classic Catalog zFS</h3> 

The Metadata Catalog is conceptually similar to the Db2 catalog ( SYSTABLES, SYSCOLUMNS etc… ). 
Not only does it store metadata about IMS Tables, but it also contains the details of the IMS to relational model mapping. 

The Metadata catalog is deployed as a zFS which needs to be mounted at the path specified for “CATPATH” in CECCUSPC. 

The zFS is allocated with generated job CECCRZCT. 
The VSAM cluster is defined in the first job step as <code>CCDC.I1.ZFS</code>, and the zFS is formatted in the second job step. 
Review the submit <code>CCDC.I1.USERSAMP(CECCRZCT)</code> to define the Metadata Catalog. 

``` 
 DEFINE CLUSTER ( -    
   NAME(CCDC.I1.ZFS) - 
   LINEAR -            
   MEGABYTES(150 128) -
   VOLUMES(*) -        
   SHAREOPTIONS(3 3)  -
   CISZ(4096))         
``` 

and

```   
//FORMAT EXEC PGM=IOEAGFMT,REGION=0M,       
//    COND=(0,LT),                          
//    PARM='-aggregate CCDC.I1.ZFS  -compat'
```



<h3 id="4.6">4.6 Define and Populate the Configuration Datasets</h3> 
<p>Classic CDC for IMS is governed by a number of parameters that are defined in the configuration datasets. 
Parameters governing threads, memory, tcpip ports and so forth, as documented in the knowledge centre. 
The creation and population of the configuration datasets is controlled by job CECCDCFG. Review the generated JCL and submit when happy.</p>
 
```
//CFGALLOC     EXEC PGM=IEFBR14                       
//CACCFGD      DD  DSN=&CPHLQ..CACCFGD,               
//             UNIT=&DISKU,                           
//             STORCLAS=&STGCL,                       
//             MGMTCLAS=&MGTCL,                       
//             SPACE=(TRK,(20,10)),                   
//             DCB=(RECFM=FBS,LRECL=64,BLKSIZE=27968),
//             DISP=(NEW,CATLG,DELETE)                
//CACCFGX  DD  DSN=&CPHLQ..CACCFGX,                   
//             UNIT=&DISKU,                           
//             STORCLAS=&STGCL,                       
//             MGMTCLAS=&MGTCL,                       
//             SPACE=(TRK,(10,5)),                    
//             DCB=(RECFM=FBS,LRECL=64,BLKSIZE=27968),
//             DISP=(NEW,CATLG,DELETE)                
//*                                                   
```

and the final step imports the default parameter values into the configuration datasets

```
//ALLOCATE EXEC ALLOCFG                         
//CFGINIT.SYSIN DD *                            
IMPORT,CONFIG,FILENAME=DDN:IMPORTCF(CECCDXFG)   
REPORT                                          
QUIT                                            
/*                                              
```

<h3 id="4.7">4.7 Create the Metadata Catalog</h3> 
<p>The Metadata Catalog is a zFS that holds catalog tables ( systables, syscolumns etc... ). 
The CACCATUT utility is used to create the metadata catalog tables in the Metadata Catalog. 
This is performed using job CECCDCAT. Review the generated JCL and submit when happy.</p>

```
//INIT     EXEC PGM=CACCATUT,PARM='INIT',REGION=&RGN       
//STEPLIB  DD  DISP=SHR,DSN=&CAC..SCACLOAD                 
//SYSOUT   DD  SYSOUT=&SOUT                                
//SYSPRINT DD  SYSOUT=&SOUT                                
//MSGCAT   DD  DISP=SHR,DSN=&CAC..SCACMSGS                 
//CACCAT   DD  PATHDISP=(KEEP,KEEP),                       
//             PATH='/opt/IBM/isclassic113/catalog/caccat' 
//CACINDX  DD  PATHDISP=(KEEP,KEEP),                       
//             PATH='/opt/IBM/isclassic113/catalog/cacindx'
```

<h3 id="4.8">4.8 Create the Replication Mapping Datasets</h3> 

The Replication Mapping datasets are used to hold the metadata to support subscriptions from IMS to targets like Kafka. 
These mapping datasets will be empty until you start defining subscriptions using Management Console or CHCCLP scripts. 
This is performed using job CECCDSUB. Review the generated JCL and submit when happy. 

```
 DEFINE CLUSTER                  - 
    (NAME(&CPHLQ..SUB)        -    
     RECSZ(1024 2048)            - 
     KEY(80 12)                  - 
     CYL(2 1)                    - 
     SPEED REUSE)                - 
  DATA                           - 
    (NAME(&CPHLQ..SUB.DATA)   -    
     CISZ(8192)                  - 
     FSPC(20 5))                 - 
  INDEX                          - 
    (NAME(&CPHLQ..SUB.INDEX)  -    
     CISZ(6144))                   
 /**/                              
 DEFINE CLUSTER                  - 
    (NAME(&CPHLQ..RM)        -     
     RECSZ(512 2432)             - 
     KEY(20 12)                  - 
     CYL(15 5)                   - 
     SPEED REUSE)                - 
   DATA                          - 
     (NAME(&CPHLQ..RM.DATA)   -    
      CISZ(8192)                 - 
      FSPC(20 5))                - 
   INDEX                         - 
     (NAME(&CPHLQ..RM.INDEX)  -    
      CISZ(2048))                  
/*                                 
```


<h3 id="4.8">4.9 Setup Encrypted Passwords</h3> 

Assuming that you enabled security in the CACCUSPC parameters file earlier: <code>CDCPSAFX="CACSX04"</code> you will need to setup 
encrypted passwords for connections to the Classic CDC instance. (Enabling security requires providing a password for all user access. 
The utilities used in the validation job require encrypted passwords to access the Classic data server.) 

The following steps are required to generate an encrypted password for the userid that will run jobs to access Classic CDC, and save it in a referencable PDS member.

Edit <code>CCDC.I1.USERCONF(CACPWDIN)</code>. Set the value to the TSO password (SYS1) for the User ID (IBMUSER) that you use to run the customization jobs.

![Encryp01](images/cdc/encrypt01.PNG)

Submit <code>CCDC.I1.USERSAMP(CACENCRP)</code> JCL to run the password generator utility, which updates <code>CCDC.I1.USERCONF(CACPWDIN)</code> with the encrypted value.

![Encryp02](images/cdc/encrypt02.PNG)

Edit <code>CCDC.I1.USERCONF(CACPWDIN)</code> again and copy the hex string value for the ENCRYPTED= keyword.

![Encryp03](images/cdc/encrypt03.PNG)

Edit <code>CCDC.I1.USERCONF(CACMUCON)</code> and replace the X'.ENCRYP PASSWD..' string with the hex string copied in the previous step.

![Encryp04](images/cdc/encrypt04.PNG)

Edit <code>CCDC.I1.USERSAMP(CACQRSYS)</code> and replace the second line of the member with the hex string that you copied.

![Encryp05](images/cdc/encrypt05.PNG)


<code>CCDC.I1.USERCONF(CACMUCON)</code> and <code>CCDC.I1.USERSAMP(CACQRSYS)</code> will be 
referenced by the Installation Verification Jobs, and other utility jobs. Click through the slideshow sequence below to follow the example above.


<br><hr>

<h2 id="5.0">5. Configure the z/OS Environment</h2>

Every z/OS environment will have different constraints and considerations for deployment. 
This worked example was implement on a Z Development and Test v13 environment, using the z/OS v2.4 stack provided by ADCD v13. 
The z/OS customizations that I needed to do were as follows.

<h3 id="5.1">5.1 APF Authorised Load Libraries</h3>

The Classic CDC Load Libraries need to be APF Authorized. 
With the ADCD distribution I simply added these libraries to ADCD.Z24C.PARMLIB(PROGAD)

```
/**********************************************/                      
/*  CCDC 11.4                                 */                      
/**********************************************/                      
APF ADD                                                               
    DSNAME(CCDC.SCACLOAD)                               VOLUME(USER0B)
```
	
<h3 id="5.2">5.2 TCPIP Ports</h3> 

Classic CDC for IMS is configured to listen on one port. I used the default port of 9087. 
The ADCD z/OS v2.4 distribution does not lock high ports, so I didn’t have any network administration to perform to open port 9087. 

<h3 id="5.3">5.3 RACF Started Task ID</h3> 

For the ZD&T environment I didn’t bother the define a started task ID to RACF. 
I just added the PROC to ADCD.Z24B.PROCLIB and ran it as START1.

<h3 id="5.4">5.4 Test Start the Classic CDC Server</h3> 

The Classic CDC Server has not yet been configured to attach to the various IMS interfaces that are neeed. 
However, this is a good point to start the server and resolve any problems with the Classic CDC Server.  
You can test the Classic CDC as a Job using the JCL in member CECCDSRC, and then deploy it as a started task later on.
Upon first start, you should expect to see the Classic CDC Server come up, but report failures on connection to IMS DRA

![Classic CDC Startup Messages](images/cdc/cdc01_16.png)


Further down the job output you may spot more detailed error messages and return codes which will give you more insight into any problems. 
Specifically the SpcRC codes can be particularly useful. 

![Classic CDC Startup SpcRC codes](images/cdc/cdc01_17.png)

Specifically, if you lookup SPcRC(00570082) in the Classic CDC Messages 
publication you will see the following explanation: 

<b>0x00570082</b> (5701762) The DRA initialization failed.
User response: See the system log from the data
server for more information on the cause. This is
usually a problem in either the JCL for the data server
or the task parameters for the service information entry
of the DRA service. 

It should be expected that Classic CDC will initially have errors connecting to IMS, which will be resolved when you have completed the IMS configuration work, which is 
covered in the next section.

Additionally, if you configured the CECCUSPC parameters to include an MQ target, then you are likely to experience MQ connection errors until you resolve
RACF permissions.

<br><hr>

<h2 id="6.0">6. Configure the IMS Environment</h2>

This is the section where Classic CDC differs from deploying CDC for a relational data source. 
CDC needs the following 3 things that are more complex to provide from IMS than Db2.


* SQL Access to IMS databases (for development work, and cdc initialization of target)
* Access to IMS Logs, and notifications when logs are activated and deactivated
* Additional IMS logging, with sufficient information for replication purposes.
 

<h3 id="6.1">6.1 DRA Startup Module</h3>

When a CDC Capture agent works with a relational database it uses the SQL interface to the database 
to obtain the data structures in the development phase, and to perform a full refresh in the operational phase. 
With Classic CDC for IMS we use the IMS DRA interface to achieve these tasks. 

You need to assemble a DRA Table with an assemble and link edit job. 
My example below was used to assemble and link edit a DRA Table called DFSPZP01, which is written to the IMS RESLIB.

```
//IBMUSERS   JOB (ASMDRA),'ASMDRA',                                     
//            CLASS=A,MSGCLASS=H,NOTIFY=&SYSUID                         
//ASM EXEC PGM=ASMA90,                                                  
//       PARM='DECK,NOOBJECT,LIST,XREF(SHORT),ALIGN',                   
//       REGION=4096K                                                   
//SYSLIB DD DSN=DFSF10.OPTIONS,DISP=SHR                                 
//       DD DSN=DFSF10.SDFSMAC,DISP=SHR                                 
//       DD DSN=SYS1.MACLIB,DISP=SHR                                    
//*                                                                     
//SYSUT1 DD UNIT=SYSDA,SPACE=(1700,(400,400))                           
//SYSUT2 DD UNIT=SYSDA,SPACE=(1700,(400,400))                           
//SYSUT3 DD UNIT=SYSDA,SPACE=(1700,(400,400))                           
//SYSPUNCH DD DSN=&&OBJMOD,                                             
//       DISP=(,PASS),UNIT=SYSDA,                                       
//       DCB=(RECFM=FB,LRECL=80,BLKSIZE=400),                           
//       SPACE=(400,(100,100))                                          
//SYSPRINT DD SYSOUT=*                                                  
//SYSIN DD *                                                            
PZP      TITLE 'DATABASE RESOURCE ADAPTER STARTUP PARAMETER TABLE'      
DFSPZP00 CSECT                                                          
**********************************************************************  
*        MODULE NAME: DFSPZP00                                       *  
*                                                                    *  
*        DESCRIPTIVE NAME: DATABASE RESOURCE ADAPTER (DRA)           *  
*                  STARTUP PARAMETER TABLE.                          *  
*                                                                    *  
*        FUNCTION: TO PROVIDE THE VARIOUS DEFINITIONAL PARAMETERS    *  
*                  FOR THE COORDINATOR CONTROL REGION. THIS          *  
*                  MODULE MAY BE ASSEMBLED BY A USER SPECIFYING      *  
*                  THEIR PARTICULAR NAMES, ETC. AND LINKEDITED       *  
*                  INTO THE USER RESLIB AS DFSPZPXX.  WHERE XX       *  
*                  IS EITHER 00 FOR THE DEFAULT, OR ANY OTHER ALPHA- *  
*                  NUMERIC CHARACTERS.                               *  
*                                                                    *  
**********************************************************************  
         EJECT                                                          
         DFSPRP DSECT=NO,                                              X
               DBCTLID=IVP1,                                           X
               DDNAME=CCTLDD,                                          X
               DSNAME=DFSF10.SDFSRESL,                                 X
               MAXTHRD=99,                                             X
               MINTHRD=10,                                             X
               TIMER=60,                                               X
               USERID=,                                                X
               CNBA=10,                                                X
               FPBUF=,                                                 X
               FPBOF=,                                                 X
               TIMEOUT=60,                                             X
               SOD=A,                                                  X
               AGN=                                                     
         END                                                            
/*                                                                      
//LNKEDT EXEC PGM=IEWL,                                                 
//       PARM='LIST,XREF,LET,NCAL'                                      
//SYSUT1 DD UNIT=SYSDA,SPACE=(1024,(100,50))                            
//SYSPRINT DD SYSOUT=*                                                  
//SYSLMOD  DD DSN=DFSF10.SDFSRESL,DISP=SHR                              
//SYSLIN   DD DISP=(OLD,DELETE),DSN=&&OBJMOD                            
//         DD DDNAME=SYSIN                                              
//SYSIN    DD *                                                         
   NAME  DFSPZP01(R)                                                    
/*                                                                      
```

Download JCL from github repository (opens new tab) <a href="https://github.com/zeditor01/recipes/blob/main/samples/cdc/asmbldra.jcl" target="_blank">here.</a> 

<b>Note the deliberate mistake! </b> 

* We just generated a DRA Table with suffix 01 <code>NAME DFSPZP01 (R)</code></p>
* Back in section 4.2 I coded my parameters member with suffix 00 <code>IMSDRASX="00"</code>
 

That "mistake" serves to force us to review how to change the Classic CDC Service parameters. 
One way of changing service parameters is by modifying the started task. The MTO commands below 
show example of how to edit the DRA Userid and the DRA Suffix 

```
F CCDC,SET,CONFIG,SERVICE=IMSDRA,DRATABLESUFFIX=’01’
F CCDC,SET,CONFIG,SERVICE=IMSDRA,DRAUSERID=’IBMUSER’
```

Another way of changing the Classic CDC Service paramters is by using the Classic Data Architect IDE, which is 
shown later on in section 7.2 of this paper.

<h3 id="6.2">6.2 IMS Logging and Notification Exits</h3> 

When a CDC Capture agent works with a relational database, it normally only has a single database log to deal with. 
IMS can be different, with multiple regions, the (small) possibility of DLI Batch jobs and so forth. 
So, Classic CDC for IMS requires that IMS tells it about log activations, deactivations and switches. 
This is done with the <b>IMS Partner Program User Exit</b> (PPUE). 

A modified PPUE must be created and deployed into IMS RESLIB. 
The module CEC1OPT must be assembled, and contain the TCPIP Log Reader Notification Port 
that was defined in the CECCUSPC parameters module <code>IMCPNPRT="5003"</code>.

A sample assembler source module (CECE1OPT) is provided for the PPUE here :  <code>CCDC.I1.USERSAMP(CECE1OPT)</code>.

You need to edit the module to suit your environment. 
In the worked example below the TCPIP address of the z/OS system was 192.168.1.191 and 
Classic CDC Server is configured to listen on port 5003. 

```
**********************************************************************  
*                                                                    *  
* NAME:        CECE1OPT                                              *  
*                                                                    *  
* DESCRIPTION: CHANGE CAPTURE RECOVERY AGENT MESSAGES AND OPTIONS    *  
*                                                                    *  
**********************************************************************  
CECE1OPT CSECT                                                          
CECE1OPT AMODE 31                                                       
CECE1OPT RMODE ANY                                                      
         DC    A(VECTORS)                                               
         DC    C'CECE1OPT',C' '   |                                     
         DC    C'&SYSDATE',C' '   |                                     
         DC    C'&SYSTIME',C' '   |                                     
         DS    0H                                                       
VECTORS  DC    A(PFXOPTS)         | PREFIX OPTIONS                      
VERSION  DC    AL2(1)             | VERSION                             
**********************************************************************  
* Set RESPTOUT (The number of seconds for notification time-out).    *  
* It must contain a number from 1 to 60. The Default is 30 seconds.  *  
**********************************************************************  
RESPTOUT DC    AL2(30)            | 30 seconds = DEFAULT                
*                                                                       
         DC    6A(0)              | RESERVED FOR FUTURE USE             
PFXOPTS  DS    0F                                                       
         DC    AL2(PFXOPTSL)      | LENGTH OF OPTION AREA               
**********************************************************************  
**********************************************************************  
* Set IPVSN to 4 to connect to the server using IPv4.                *  
* Set IPVSN to 6 to connect to the server using IPv6.                *  
**********************************************************************  
IPVSN    DC    AL1(4)             | 4=IPv4, 6=IPv6                      
         DC    AL1(0)             | Reserved                            
                                                                        
**********************************************************************  
* The IPV6 field is ignored when IPVSN is set to 4, and the          *  
* IPV4 field is ignored when IPVSN is set to 6.                      *  
*                                                                    *  
* The IPv4 address format is 0.0.0.0.                                *  
*                                                                    *  
* The IPv6 address format is 9:3155:3155:3155:0:0:0:0.               *  
**********************************************************************  
TCPADDR4 DC    AL1(192),AL1(168),AL1(1),AL1(191)   IPv4 addr            
TCPADDR6 DC    AL2(9)               IPv6 addr - 1st 16 bits             
         DC    AL2(3155)            IPv6 addr - 2nd 16 bits             
         DC    AL2(3155)            IPv6 addr - 3rd 16 bits             
         DC    AL2(3155)            IPv6 addr - 4th 16 bits             
         DC    AL2(0)               IPv6 addr - 5th 16 bits             
         DC    AL2(0)               IPv6 addr - 6th 16 bits             
         DC    AL2(0)               IPv6 addr - 7th 16 bits             
         DC    AL2(0)               IPv6 addr - 8th 16 bits             
**********************************************************************  
* Set the PORT field to the port number (decimal)                    *  
**********************************************************************  
PORT     DC    AL2(5003)                     TCP PORT                   
PFXOPTSL EQU   *-PFXOPTS                                                
         END                                                            
```		 
	
The CECE1OPT module was assembled and linkedited using the job below

```
//IBMUSERN JOB 1,IBMUSERN,MSGCLASS=A,MSGLEVEL=(1,1),  
//         CLASS=A,NOTIFY=$SYSUID                     
//ASM EXEC PGM=ASMA90,                                
//       PARM='DECK,NOOBJECT,LIST,XREF(SHORT),ALIGN', 
//       REGION=4096K                                 
//SYSLIB DD DSN=DFSF10.OPTIONS,DISP=SHR               
//       DD DSN=DFSF10.SDFSMAC,DISP=SHR               
//       DD DSN=SYS1.MACLIB,DISP=SHR                  
//*                                                   
//SYSUT1 DD UNIT=SYSDA,SPACE=(1700,(400,400))         
//SYSUT2 DD UNIT=SYSDA,SPACE=(1700,(400,400))         
//SYSUT3 DD UNIT=SYSDA,SPACE=(1700,(400,400))         
//SYSPUNCH DD DSN=&&OBJMOD,                           
//       DISP=(,PASS),UNIT=SYSDA,                     
//       DCB=(RECFM=FB,LRECL=80,BLKSIZE=400),         
//       SPACE=(400,(100,100))                        
//SYSPRINT DD SYSOUT=*                                
//SYSIN DD DSN=CCDC.I1.USERSAMP(CECE1OPT),DISP=SHR    
//LNKEDT EXEC PGM=IEWL,                               
//       PARM='LIST,XREF,LET,NCAL'                    
//SYSUT1 DD UNIT=SYSDA,SPACE=(1024,(100,50))          
//SYSPRINT DD SYSOUT=*                                
//SYSLMOD  DD DSN=DFSF10.SDFSRESL,DISP=SHR            
//SYSLIN   DD DISP=(OLD,DELETE),DSN=&&OBJMOD          
//         DD DDNAME=SYSIN                            
//SYSIN    DD *                                       
   NAME  CECE1OPT(R)                                  
/*                                                    
```

<h3 id="6.3">6.3 Augment DBD to generate T99 Log Records</h3> 

A third difference between Classic CDC for IMS and CDC for a relational source is IMS logging. 

In order to perform data replication it is necessary that the database is instructed to 
write log records with all the before and after images in the log record. 
(Normally databases only write sufficient data into the log records to support recovery). 

For Db2 databases, the log records can simply be expanded for the tables of interest, by altering 
the definition, with DDL such as: 
 
<code>ALTER TABLE MYSCHEMA.MYTABLE DATA CAPTURE CHANGES</code>.

IMS is different. IMS generates Type 50 log records by default. 
In order to support data replication you need to augment the IMS database to write Type 99 log records, 
which are designed for the purposes of data replication.
This requires the IMS database to be “augmented” with an EXIT clause to specify the content 
to be written to the Type 99 log records. 

This worked example used the IBM-provided sample database (DI21PART) as the CDC source database. 
The DBD source is stored in <code>DFSF10.SDFSISRC(DI21PART)</code>. 
This example only adds the EXIT clause to three specific segments (PARTROOT, STANINFO, STOKSTAT). 
You can choose to augment whichever segments you want, or you could augment the entire database on the DBD statement. 
This DBD source in this worked example was edited to specify Type 99 logging as follows. 

```
         DBD   NAME=DI21PART,ACCESS=(HISAM,VSAM)                        
      DATASET  DD1=DI21PART,DEVICE=3380,OVFLW=DI21PARO,                X
               SIZE=(2048,2048),RECORD=(678,678)                        
      SEGM     NAME=PARTROOT,PARENT=0,BYTES=50,FREQ=250,               X
               EXIT=(*,NOKEY,DATA,NOPATH,(NOCASCADE))                   
      FIELD    NAME=(PARTKEY,SEQ),TYPE=C,BYTES=17,START=1               
      SEGM     NAME=STANINFO,PARENT=PARTROOT,BYTES=85,FREQ=1,          X
               EXIT=(*,KEY,DATA,NOPATH,(NOCASCADE))                     
      FIELD    NAME=(STANKEY,SEQ),TYPE=C,BYTES=2,START=1                
      SEGM     NAME=STOKSTAT,PARENT=PARTROOT,BYTES=160,FREQ=2,         X
               EXIT=(*,KEY,DATA,NOPATH,(NOCASCADE))                     
      FIELD    NAME=(STOCKEY,SEQ),TYPE=C,BYTES=16,START=1               
      SEGM     NAME=CYCCOUNT,PARENT=STOKSTAT,BYTES=25,FREQ=1            
      FIELD    NAME=(CYCLKEY,SEQ),TYPE=C,BYTES=2,START=1                
      SEGM     NAME=BACKORDR,PARENT=STOKSTAT,BYTES=75,FREQ=0            
      FIELD    NAME=(BACKKEY,SEQ),TYPE=C,BYTES=10,START=1               
      DBDGEN                                                            
      FINISH                                                            
      END                                                               
```
  
After changing the DBD, an ACBGEN is required to cause the changes to be implemented. 
The following job was submitted to perform the ACBGEN.

```
//IBMUSERS   JOB (CRTZFS),'CRTZFS',                      
//            CLASS=A,MSGCLASS=H,NOTIFY=&SYSUID          
//USRPROC     JCLLIB ORDER=(DFSF10.SDFSPROC)             
//IVPDB1    EXEC PROC=DFSDBDGN,SOUT='*',MBR=DI21PART,    
//          NODE1='DFSF10',NODE2='DFSF10',SYS2=''        
//C.SYSIN   DD DISP=SHR,DSN=DFSF10.SDFSISRC(&MBR)        
//IVPPSB    EXEC PROC=DFSPSBGN,SOUT='*',MBR=DFSSAM14,    
//          NODE1='DFSF10',NODE2='DFSF10',SYS2=''        
//C.SYSIN   DD DISP=SHR,DSN=DFSF10.SDFSISRC(&MBR)        
//IVPACB    EXEC PROC=ACBGEN,SOUT='*',                   
//          NODE1='DFSF10',NODE2='DFSF10',SYS2=''        
//G.SYSIN   DD *                                         
  BUILD DBD=DI21PART                                     
/*                                                       
//ACBLIBA  EXEC PROC=OLCUTL,SOUT='*',TYPE=ACB,IN=S,OUT=A,
//          NODE1='DFSF10',NODE2='DFSF10',SYS2=''        
//ACBLIBB  EXEC PROC=OLCUTL,SOUT='*',TYPE=ACB,IN=S,OUT=B,
//          NODE1='DFSF10',NODE2='DFSF10',SYS2=''        
```
  
<h3 id="6.4">6.4 Copy the IBM-Supplied Exits into IMS RESLIB</h3> 

Having made the changes to support the Partner Program Notification interface, and the Enhanced IMS database logging, 
you need to copy the IBM-Supplied Exits from CCDC.SCACLOAD into IMS RESLIB. 

1. Copy CCDC.SCACLOAD(SDFSRESL) to IMS RESLIB, or a concatenated library.
2. Copy DFSPPUE0 from wherever you created in to IMS RESLIB, or a concatenated library.


That's it for the Classic CDC Server !!! 

<br><hr>

<h2 id="7.0">7. Integrate with the wider CDC Landscape</h2>

Now that the mainframe CDC Capture Server is ready for business, you will need to start using some non-mainframe tools in order 
to define "IMS Tables", and define Subscriptions from them to target systems. Section 7 covers this matters. 

<h3 id="7.1">7.1 Deploy Classic CDC as a Started Task</h3> 

Assuming you want to run Classic CDC as a started task, rather than a batch job, you should copy 
the contents of the JCL to run Classic CDC as a batch job <code>CCDC.I1.USERSAMP(CECCDSRC)</code> 
to your PROCLIB, and follow your site standards for establishing a new started task. 

<h3 id="7.2">7.2 Deploy and use the Classic Data Architect IDE</h3>

The next step is to create some "IMS Tables" to be used as CDC replication source objects. 

The Classic CDC Metadata catalog contains the mapping information from hierarchical IMS structures to the 
a relational projection, as is required to participate in CDC data replication. The mapping process is enabled 
by using an Eclipse-based tool called the Classic Data Architect to define the data mapping. 

The current release of the Classic Data Architect tool should be downloaded from IBM fix Central. 
Installation to Windows is a standard setup.exe style of installer. 

Once CDA is installed, the tasks you will need to perform with it are as follows: 


1. Configure the connection to the Classic CDC started task
2. Create a data development project, and Import DBDs and Copybooks for the mapping work
3. Develop a relational model "IMS Table" based on the imported DBDs and copybooks 
4. Deploy the "IMS Table" to the Classic CDC for IMS Started Task
5. Test the validity of the IMS Table Mapping using SQL through the CDA
6. Make the IMS Table available for use as a source for CDC subscriptions 


<h4>7.2.1 Configure the connection to the Classic CDC started task</h4>

Start up the CDA and choose a workspace on your PC filesystem. 

Verify that you are using the "Data Perspective" from the Action Bar : Window - Open Perspective - Data. 

The Data Perpective is shown in the screenshot below.  


<b>Top-Left Window</b> : The project explorer
<b>Top-Middle Window</b> : Working subject pane (contents depend on what objects are selected)
<b>Bottom-Left Window</b> : Data Source Explorer (data soucre data access) and Console Explorer (data source configurations).
 
![CDA Data Perspective](images/cdc/CDA_Data_Perspective.png)

In order to make a connection to the Classic CDC Server you must open up the "Data Source Explorer" 
(bottom left window in the Data Perspective), right click on "Database Connections" and choose "New". 
Then fill in the connection details ( 192.168.1.191:9087 IBMUSER/SYS1 ), test the connection and press OK.

![CDA Connection](images/cdc/CDA_Connect.png)

You must also repeat this process to define a connection on the "Console Explorer" window. Defining a connection in one window does 
not copy that connection to the other window. 

If the connection fails, the most likely cause is a firewall. Once you have eliminated firewalls, 
check in the Classic CDC started task output for correction errors.  

Once connected, you can expand the "SAMPLEDS" datasource and explore the schemas, and the table objects within the schemas. 
Initially, the Catalog Tables will be visible under schemas "SYSIBM" and "SYSCAC". When you have developed some IMS Tables" 
they will be visible under the schema that they were created in. 

![CDA Data Source Explorer](images/cdc/CDA_Data_Source.png)

Click on the Console explorer tab now, and expand the Services and Configuration Tabs. 
If you select a configuration (such as the DRA Service configuration) and edit the properties, you can make parameter changes here (such as DRATABLESUFFIX=01). 

![CDA Imported Artefacts](images/cdc/CDA_Console_Parms.png)

<h4>7.2.2 Create a data development project, and Import DBDs and Copybooks for the mapping work</h4>

Having configured CDA to operate with the Classic CDC server, the next task is to define an IMS Table. 
This is a multi-step graphical dialog, that enables you to use DBDs and Copybooks to create a relational 
projection of data slices from the IMS hierarchical database. 


Go to the "Data Project Explorer" (top left window) and create a new project, such as "Zeditor". The steps you need to follow are described in the list below, and illustrated in the slideshow below that. 


1. Create a Data Development Project.
2. Create a physical data model (Classic Integration) within that project.
3. Import the IMS DBDs and Copybooks that describe the IMS database.
4. Create an IMS Table mapping based on the imported DBDs and Copybooks.
5. Generate DDL to create that IMS Table definition.
6. Execute the DDL against the target Classic CDC server.
7. Verify the generated object


Scroll through the screenshots below to following the GUI dialog to Define and Verify an IMS Table.

Screenshot 1 of 17 : Create a new Data Design Project (Zeditor)
![CDA Imported Artefacts](images/cdc/imstab01.PNG)

Screenshot 2 of 17 :  Inside the project, create a new Physical Data Model
![CDA Imported Artefacts](images/cdc/imstab02.PNG)

Screenshot 3 of 17 :  Use the Classic Integration template for this data model
![CDA Imported Artefacts](images/cdc/imstab03.PNG)

Screenshot 4 of 17 :  Highlight IMS DBDs, right mouse click, import files
![CDA Imported Artefacts](images/cdc/imstab04.PNG)

Screenshot 5 of 17 :  Import either from 3270 or Local PC, and Review artefact
![CDA Imported Artefacts](images/cdc/imstab05.PNG)

Screenshot 6 of 17 :  Repeat process to import COBOL Copybooks
![CDA Imported Artefacts](images/cdc/imstab06.PNG)

Screenshot 7 of 17 :  Highlight the Physical Data Model, Create an IMS Table
![CDA Imported Artefacts](images/cdc/imstab07.PNG)


Screenshot 8 of 17 :  Specify the DBD, assign the desired Schema Name
![CDA Imported Artefacts](images/cdc/imstab08.PNG)

Screenshot 9 of 17 :  Specify Segments, SSID, PSB, and choose Table Name and Usage
![CDA Imported Artefacts](images/cdc/imstab09.PNG)

Screenshot 10 of 17 :  Specify root segment copybook and choose fields
![CDA Imported Artefacts](images/cdc/imstab10.PNG)

Screenshot 11 of 17 :  Specify dependent segment copybooks and choose fields
![CDA Imported Artefacts](images/cdc/imstab11.PNG)

Screenshot 12 of 17 :  Review the IMS table mapping, and Finish
![CDA Imported Artefacts](images/cdc/imstab12.PNG)

Screenshot 13 of 17 :  Highlight the Mapped Object, right mouse click and Generate DDL
![CDA Imported Artefacts](images/cdc/imsgen01.PNG)

Screenshot 14 of 17 :  Specify Artefacts required
![CDA Imported Artefacts](images/cdc/imsgen02.PNG)

Screenshot 15 of 17 :  Review DDL, and tick to execute DDL on Classic CDC Server
![CDA Imported Artefacts](images/cdc/imsgen03.PNG)

Screenshot 16 of 17 :  Specify the target server (SAMPLEDS) and Finish
![CDA Imported Artefacts](images/cdc/imsgen04.PNG)

Screenshot 17 of 17 :  Review object in Data Source Explorer for accuracy
![CDA Imported Artefacts](images/cdc/imsgen05.PNG)

 
  
<br>

<h4>7.2.3 Test the validity of the IMS Table Mapping using SQL through the CDA</h4>

Once you have deployed the IMS Table to the Classic CDC Server, you should validate that the table mapping is correct. 
The simplest way to do this is to run an SQL query against the mapped IMS Table. 
Right Click on the Table, and either "return all rows" or choose "sample contents" to eyeball the data for validity 


![CDA Verify Data](images/cdc/cdc01_31.png)

You can dig deeper into data validation with more probing SQL through the Classic Data Architect. 
Just click the SQL icon in the Data Source Exporer window (bottom-right) and SQL away. 

Be mindful of the fact that many test IMS systems have "dirty data". 
If this is true for your environment, you can overcome dirty data with some forgiving Classic CDC parameters. 
However, at some point before deploying to production, you must take responsibility for validating the data and the accuracy of the mapping. 

The "forgiving" Classic CDC parameters that are available to you are as follows 


| Parameter | Purpose |
| --- | --- |
| DATACONVERRACT | 1 will convert invalid numeric data to -9s for query actions |
| CSDATAVALIDATEAC | 1 will convert invalid numeric data to -9s for change data capture actions | 




Deploying the IMS Table <b>(with Change Capture Table Usage)</b> makes it visible to the CDC Management Console and Access Server 
when they connect to the Classic CDC Server

<h3 id="7.3">7.3 Connect from Management Console to Classic CDC Started Task</h3>

This document is primarily concerned with everything that needs to be done to establish Classic CDC for IMS as a CDC source.

Using the the CDC administration tools is now a standard CDC task which is covered in 
the [10. Creating and Operating CDC Subscriptions.](C010_administration.md) paper.


<h3 id="7.4">7.4 Use CHCCLP Scripting for z/OS</h3>

CDC Replication is traditionally a Windows-centric environment for operations and control, but it also has advanced scripting capabilities for automation. 

The CDC Management Console is a comprehensive GUI that addresses all parts of the devops 
lifecycle ( access control, definition, operations, monitoring ). 

The CHCCLP Scripting tools offer automated devops controls using scripts. These can be executed from the Windows-based 
Management Console, or from the Access Server on Windows or Linux. 
The CHCCLP scripting option will be attractive to all shops that wish to implement strong devops governance and control to their 
CDC replication environments. Shops with a z/OS operation bridge should know that the CHCCLP scripting environment can also be deployed 
inside z/OS, either from unix system services (USS) or from JCL (using the java batch scheduler).

All of these devops options are covered in the the [11. Devops Options for CDC.](C011_devops.md paper 
and [12. CHCCLP Scripting.](C012_chcclp.md) paper in this series of articles. 

<h3 id="7.5">7.5 Conforming to site standards for cross-platform devops and security.</h3> 

So far, this document has been primarily concerned with the mechanics of making CDC operate from IMS to any number of heterogeneous targets. 
It may be hard to believe, but that was the easy part! 

The author has worked with several mainframe customers deploying CDC for IMS to feed a stream of changes to midrange and Cloud targets. 
The challenges to overcome will include... 

* different development and operational teams supporting the capture and apply services
* co-ordinating devops tasks between cross-platform teams
* change control procedures
* implementing TLS encryption between the application-transparent z/OS platform and application-controlled LUW platforms


A good approach is to start by considering the non-functional requriements for the business service that CDC will support. If the business 
requires a high level of service ( low latency, stringent monitoring and alerting, minimal downtime, fast recovery from outages etc... ) then
an operational support model can be developed to meet those requirements. 


Once the required service levels are defined, that is a useful reference point for assessing whether the opertional 
management controls and interfaces between different operations teams can satisfy those service levels 

* In some cases, the co-operation between different operational teams can be adjusted to satisfy the required service levels 
* In other cases, it may be helpful to use technology options to shift the CDC operations entirely to mainframe, or entirely to non-mainframe. 
This case be done by selecting different CDC agents in many cases, as follows 

1. If a Windows/Linux operations hub is desired, then there are remote capture agent options for VSAM and DB2 z/OS.
2. If a z/OS operations hub is desired, then the Linux-based CDC agents can be deployed as software containers inside z/OS Container Extensions

Please be aware of the flexible CDC deployment options that exist, and take an early view on what choices may provide the best 
devops lifecycle proposition for your organisation. 

This series of articles includes a heavy focus of worked deployment examples, but the articles in the "Using CDC" column do aim 
to address the practical devops challenges with recommendations on how to address common challenges.
