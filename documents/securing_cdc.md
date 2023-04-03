# Securing CDC with TLS and LDAP

This paper addresses the likely security requirements for any CDC deployment, and 
provides worked examples of how to satisfy those requirements with a CDC solution.

## Contents

1. Authentication and Encryption Options for CDC Components 
2. Configuring Authentication
3. Encryption between Management Console and Access Server 
4. Encryption between Access Server and CDC Agents
5. 5. Worked Example of mTLS between z/OS Capture agent and LUW Apply Agent
6. Operational Considerations arising from using Encryption


## Security Context

In recent years cyber security has become a non-negotiable requirement of any system that communicates over a network, regardless of whether it is a public or a private network. This document provides worked examples of

* Implementing LDAP authentication for CDC users. 
* Implementing transport layer security (mutually authenticated encryption) between CDC capture and apply agents on any platform. 
* Implementing transport layer security (server authenticated encryption) between Management Console and Access Server.
* It does not attempt to cover all the product's secuirty features.
* Comprehensive details of the product's features are covered in <a href="https://www.ibm.com/docs/en/idr/11.4.0">IBM CDC knowledge centre.</a>
 

## 1. Authentication and Encryption Options for CDC Components 

Lets start with a broad view of connectivity between CDC components, and consider what security provisions are approapriate.

### 1.1 CDC Component Reference Scenarios

The diagram below is a generic representation of the four CDC components that you probably want to consider.

![CDC Components](/images/cdc/cdcsec01.png)

The basic communcations processes that occur between these components are

1. Management Console connects to Access Server to invoke administration tasks.
2. Access Server connects to CDC Capture and Apply Agents to define/operate subscriptions.
3. CDC Capture agent connects to CDC Apply agent over TCPIP to stream change data packets. 


### 1.2 Authentication options for CDC Users 

Moving on to Authentication options, there are several options, as depicted in the diagram below.

![CDC Components](/images/cdc/cdcsec02.png)

Users of the Windows Management Console must be authenticated by the Access Server. There are two options for this
1. Access Server stores userid/password credentials in an encrypted local file.
2. Access Server performs an authentication check using LDAP protocols.

If you use the LDAP option, then you have two further options.
1. Authentication only, where an authenticated user can then perform any CDC Access Server tasks.
2. Authentication and Authorisation, where specific privileges against specific CDC datastores are controlled.

Whichever LDAP option you choose, the CDC agent itself is subject to whatever authentication and authorisation controls are
implement at the various data sources. Whether z/OS or Linux, this would normally be a mixture of OS Authentication 
and associated DBMS authorisation.

It is possible to use an "embedded" Access Server, that is installed as part of the Management console, as depicted below.

![CDC Components](/images/cdc/cdcsec03.png)

This still conforms to exactly the same authentication options as a separate access server. The embedded access server option 
may be attractive depending on the way that CDC will be operated and managed in a production environment. 

* A site that wants to automate CDC operations with CHCCLP scripting would prefer a separate access server as part of the production infrastructure
* A site that uses z/OS system automation extensively may prefer to run CHCCLP scripts under JCL, and avoid the need to provision, secure and operate a separate server in the production environment.

There is no single right answer here. CDC is designed to operate in wildly heterogeneous environments. It tries not to be prescriptive on 
how operations and system automation is achieved because every site will be different. Hence - the provision of different options.


### 1.3 Encryption options between CDC Components 
 
Encryption of sensitive business data over a network should always be encrypted. CDC supports industry-standard Transport Layer Security 
standards, as depicted in the diagram below.

![CDC Components](/images/cdc/cdcsec04.png)

All communications between CDC agents and CDC Access Server use mutual-authenticated TLS encryption (mTLS). This is based on 
certificates being used as the basis for negotiating encryption on CDC connections.

The CDC knowledge center describes an approach using self-signed certificates between the various end points. However, it would 
be more common for an enterprise to use a certificate authority to sign certificates to be stored at each end point. The worked 
examples in this paper are based on certificate authority.

#### 1.3.1 Application-Controlled and Application-Transparent TLS 

The implementation of TLS encryption is quite different between z/OS and other platforms.

* Linux, Unix and Windows platforms leave the application to manage the encryption process.
* z/OS as a platform supports "Application-Transparent TLS" (AT-TLS)

Consequently, CDC components conform to the standards of their respective operating systems.

* CDC components on LUW platforms implement Application-Controlled TLS, and use an encryption profile to manage the mTLS processing.
* CDC components on the z/OS platforms are completely ignorant of AT-TLS processing. As z/OS service ( Policy Agent ) is configured to intercept selected communications (eg: on specified TCPIP ports) and enforce TLS encryption.

Both approaches implement exactly the same standards-based TLS, but the configuration is different.

#### 1.3.2 Management Console TLS encryption 

The windows Management Console supports server-authenticated TLS, which is simpler, and sufficient for a simple Graphical client to the Access Server

 

## 2. Configuring Authentication 

This sections provides worked example of the three configuration options for authentication using a separate Access Server.
Should you wish to deploy an embedded Access Server, the principles in the examples below should be enough to see you right.

***Important Note:*** These three options are mutually exclusive for an installation of the access server. 
You must decide which option you want to use, and choose the appropriate option at installation time.

### 2.1 Authentication without LDAP 

Authentication without LDAP is easy. It's probably a good option if you are assessing CDC for it's replication capabilities without the effort of configuring a hardened operational production service.
The steps are

1. Install Windows Management Console without the embedded access server.
2. Install the Access Server without the LDAP Option.
3. Start the access server and create the CDC SYSADMIN userid. (dmcreateuser command)
4. Start the Management Console, and login to the Access Server (specifying TCPIP address & port)

The result of these steps will be that Access Server uses an encrypted local file to hold userids and passwords.

Screenshots from the sequence of steps follow

***Step 1:*** Leave the "LDAP Embedded Access Server" box <u>unchecked</u> if you want to connect to a separate Access Server.

![cdc_mc_install_noldap](/images/cdc/cdc_mc_install_noldap.png)

***Step 2:*** Installation dialog to install the Access Server (on Linux) without the LDAP option.

```
$ unset DISPLAY
$ ls
iidraccess-11.4.0.4-11072-linux-x86-setup.bin  setup-iidr-11.4.0.4-5618-linux-x86.bin
$ ./iidraccess-11.4.0.4-11072-linux-x86-setup.bin
Preparing to install
Extracting the JRE from the installer archive...
Unpacking the JRE...
Extracting the installation resources from the installer archive...
Configuring the installer for this system's environment...

Launching installer...

===============================================================================
Choose Locale...
----------------

    1- Deutsch
  ->2- English
    3- Español
    4- Italiano
    5- Português  (Brasil)

CHOOSE LOCALE BY NUMBER: 2
===============================================================================
IBM InfoSphere Data Replication Access Server    (created with InstallAnywhere)
-------------------------------------------------------------------------------

Preparing CONSOLE Mode Installation...




===============================================================================
Introduction
------------

InstallAnywhere will guide you through the installation of IBM InfoSphere Data
Replication Access Server.

It is strongly recommended that you quit all programs before continuing with
this installation.

Respond to each prompt to proceed to the next step in the installation.  If
you want to change something on a previous step, type 'back'.

You may cancel this installation at any time by typing 'quit'.

PRESS <ENTER> TO CONTINUE:



===============================================================================
Choose Install Folder
---------------------

Where would you like to install?

  Default Install Folder: /opt/IBM/InfoSphereDataReplication/AccessServer

ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT
      :



===============================================================================




    LICENSE INFORMATION

    The Programs listed below are licensed under the following License
    Information terms and conditions in addition to the Program license
    terms previously agreed to by Client and IBM. If Client does not have
    previously agreed to license terms in effect for the Program, the
    International Program License Agreement (Z125-3301-14) applies.

    Program Name (Program Number):
    IBM InfoSphere Data Replication Management Console / Access Server v11.
    4.0.4 (Tool)
	
    The following standard terms apply to Licensee's use of the Program.

    Limited use right

    With the exception of Bundled Programs, all IBM software provided to
    Licensee with the Program can only be used to support Licensee's use
    of the Principal Program under this License Information document,

Press Enter to continue viewing the license agreement, or enter "1" to
   accept the agreement, "2" to decline it, "3" to print it, or "99" to go back
   to the previous screen.: 1




===============================================================================
Enable LDAP Configuration
-------------------------

Enable and select an LDAP configuration.
  ->1- None (Standard Mode)
    2- LDAP Authentication Only
    3- LDAP Authentication & Authorization
    4- LDAP CHCCLP Embedded

CHOOSE LDAP CONFIGURATION BY NUMBER, OR PRESS <ENTER> TO ACCEPT THE DEFAULT
   : 1



===============================================================================


Enter the TCP/IP port for Access Server.
Port Number: (Default: 10101):



===============================================================================
Configure User Data Folder
--------------------------

Access Server requires a folder to store logs, configuration information and
user data. Specify a folder where this information should be stored.


Where would you like your user data folder?

Default User Data Folder: /opt/IBM/InfoSphereDataReplication/AccessServer


   ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT:



===============================================================================
Pre-Installation Summary
------------------------

Please Review the Following Before Continuing:

Product Name:
    IBM InfoSphere Data Replication Access Server

Install Folder:
    /opt/IBM/InfoSphereDataReplication/AccessServer

Link Folder:
    /home/cdcinst1

User Data Folder:
    /opt/IBM/InfoSphereDataReplication/AccessServer

Disk Space Information (for Installation Target):
    Required:  359,173,263 Bytes
    Available: 932,703,215,616 Bytes

PRESS <ENTER> TO CONTINUE:



===============================================================================
Installing...
-------------

 [==================|==================|==================|==================]
 [------------------|------------------|------------------|------------------]



===============================================================================
Installation Complete
---------------------

Congratulations. IBM InfoSphere Data Replication Access Server has been
successfully installed to:

/opt/IBM/InfoSphereDataReplication/AccessServer

Before you connect to this Access Server installation, you must start Access
Server and create the administration user account. See the installation guide
for more information. You should also install the equivalent version of IBM
InfoSphere Data Replication Management Console, if you haven't already done
so, before connecting to Access Server.


PRESS <ENTER> TO EXIT THE INSTALLER:
```

***Step 3:*** Start the access server and create the CDC SYSADMIN userid. logon as the cdc install userid, switch to the access server /bin directory, start the 
access server with the ```dmaccessserver``` command, and then create the access server sysadmin id with the ```dmcreateuser``` command.

```
$ pwd
/opt/IBM/InfoSphereDataReplication/AccessServer/bin
$
$ ls
asnclp                      dmchangeuserpassword  dmdeleteuser          dmlistuserdatastores  dmterminateuser
chcclp                      dmcreatedatastore     dmdisableuser         dmlistusers           dmunlockuser
dmaccessserver              dmcreateuser          dmenableuser          dmresetuser
dmaddconnection             dmdeleteconnection    dmlistdatastores      dmshowversion
dmchangeconnectionpassword  dmdeletedatastore     dmlistdatastoreusers  dmshutdownserver
$
$ ./dmaccessserver &
$
$ ./dmcreateuser cdcadmin cdcadmin cdcadmin l0nep1ne sysadmin true false false
$
```

***Step 4:*** Start the Management Console, and login to the Access Server (specifying TCPIP address & port)

![cdc_mc_connect_as](/images/cdc/cdc_mc_connect_as32.png)

Now you can connect to CDC datastores. Go to the Access Manager tab, click on "new connection" and fill in the details.

![cdc_mc_connect_as34](/images/cdc/cdc_mc_connect_as34.png)


### 2.2 Authentication-Only with LDAP 

Using the same Windows Management Console that was installed in the previous step, we would install a different Access Server with the LDAP Autentication-Only Option.

As a ***pre-requsuite*** you need to setup an LDAP Server which has cdcBaseDN=o=cdc,dc=ibm setup. 
An example of using LDAP on z/OS with an LDBM backend is described in Appendix A.


1. Install the Access Server with the LDAP Authentication-Only Option.
2. Configure the connectivity from Access Server to the LDAP Server (ldap.properties file)
3. Start the Access Server and create the root userid for the CDC Access Server. (CHCCLP command)
4. Start the Management Console, and login to the Access Server (specifying TCPIP address & port)

The result of these steps will be that Access Server uses the LDAP server for authentication, but not CDC authorisation control.

***Step 1:*** Install the Access Server with the LDAP Authentication-Only Option.

Same dialog as before, except choose Option 2 (LDAP Authentication Only)


```
===============================================================================
Enable LDAP Configuration
-------------------------

Enable and select an LDAP configuration.
  ->1- None (Standard Mode)
    2- LDAP Authentication Only
    3- LDAP Authentication & Authorization
    4- LDAP CHCCLP Embedded

CHOOSE LDAP CONFIGURATION BY NUMBER, OR PRESS <ENTER> TO ACCEPT THE DEFAULT
   : 2
```

***Step 2:*** Configure the connectivity from Access Server to the LDAP Server (ldap.properties file) 

Edit the ldap.properties file with the correct connectivity values

![ldap_properties](/images/cdc/ldap_properties.png)

***Step 3:*** Start the Access Server

```
$ pwd
/opt/IBM/InfoSphereDataReplication/AccessServer/bin
$
$ ls
asnclp                      dmchangeuserpassword  dmdeleteuser          dmlistuserdatastores  dmterminateuser
chcclp                      dmcreatedatastore     dmdisableuser         dmlistusers           dmunlockuser
dmaccessserver              dmcreateuser          dmenableuser          dmresetuser
dmaddconnection             dmdeleteconnection    dmlistdatastores      dmshowversion
dmchangeconnectionpassword  dmdeletedatastore     dmlistdatastoreusers  dmshutdownserver
$
$ ./dmaccessserver &
$
```

***Step 4:*** On the access server, create the root userid for the CDC Access Server. (CHCCLP command)

Switch to the bin directory of the Access Server installation, invoke chcclp and enter the command to add the CDC Sysadmin user.

```
$ pwd
/opt/IBM/InfoSphereDataReplication/AccessServer/bin
$
$ ls
asnclp  chcclp  dmaccessserver  dmshowversion  dmshutdownserver
$ ./dmaccessserver &
$
$ ./chcclp
Command Line Processor for IBM InfoSphere Data Replication 11.4.0 [Build 11072]
(c) Copyright IBM Corporation 2013, 2016

You can execute IBM InfoSphere Data Replication commands from the command
prompt. Use double quotes around parameter values that contain special
characters or syntax keywords, or around empty strings. If the parameter value
contains double quotes, use single quotes around the value. Commands must be
terminated with ; and can span multiple lines.

For help on the available commands or for a specific command, type:

  help;
  help "connect server";
  help "11.3";

To turn on verbose output, type:

  set verbose;

To exit interactive mode, type exit; or quit;

Repl > add ldap access manager username cdcadmin password l0nep1ne;
[ERR3006]: simple bind failed: 192.168.1.191:389
```

<h1>FIX THIS</h1> 


***Step 5:*** Start the Management Console, and login to the Access Server (specifying TCPIP address & port)

Same as before, except that this time the authentication processing is performed by LDAP calls.



### 2.3 Authentication and Authorization with LDAP 

This option seems like a dated throwback to the era of datamarts, as it envisages a hierarchy of CDC administrators performing ad-hoc subscriptions to a network of database servers.
In my experience with Banks and government entities anything that moves sensitive data around the network is tested heavily and then locked down very hard.
It conjures up a very 1970s office setting in my mind that just doesn't exist in today's cyber security world.
Having said that, it's an extra option that is available to you, if it is helpful.

The setup is very similar to the previous step.
The only setup difference is that the Access Server installation must specify the "Authentication and Authorization with LDAP" option.

```
===============================================================================
Enable LDAP Configuration
-------------------------

Enable and select an LDAP configuration.
  ->1- None (Standard Mode)
    2- LDAP Authentication Only
    3- LDAP Authentication & Authorization
    4- LDAP CHCCLP Embedded

CHOOSE LDAP CONFIGURATION BY NUMBER, OR PRESS <ENTER> TO ACCEPT THE DEFAULT
   : 3
```

The result is that CDC root user will be able to grant and revoke CDC administration priviledges in whatever level of granularity is required.



## 3. Encryption between Management Console and Access Server 
 
Lets just deal with the simple TLS connection first, before tackling the more challenging one.

The connections between Management Console and Access Server can be encrypted using Server-Authentication. 
The management console just needs to trust the certificate of the access server.

Configuring the Access Server and Management Console is easy. 
You just need to edit a tls.properties file for each of these components. 

<h1>tls.properties</h1>




## 4. Encryption between Access Server and CDC Agents   
 
<h3 id="4.1">4.1 Mutually Authenticated TLS Encryption</h3>  
<h3 id="4.2">4.2 Simplistic mTLS with Self-Signed Certificates</h3>
<h3 id="4.3">4.3 mTLS with Certificate Authorities</h3>
<h3 id="4.4">4.4 Application Controlled TLS (LUW)</h3>
<h3 id="4.5">4.5 Application Transparent TLS (z/OS)</h3>


## 5. Worked Example of mTLS between z/OS Capture agent and LUW Apply Agent 
 
<h3 id="5.1">5.1 Environment for Scenario without Encryption</h3>  
<h3 id="5.2">5.2 mTLS configuration of Access Server</h3>
<h3 id="5.3">5.3 mTLS configuration of CDC Apply on Linux</h3>
<h3 id="5.4">5.4 AT-TLS configuration of CDC Capture on z/OS</h3>
<h3 id="5.5">5.5 Certificate Exchanges between platforms</h3>
<h3 id="5.6">5.6 Operating CDC with mTLS encryption</h3>
<h3 id="5.7">5.7 Monitoring and Tracing of encrypted network flows</h3>




## 6. Operational Considerations arising from using Encryption 
 
<h3 id="6.1">6.1 Always Encrypted CDC Agents</h3>  
<h3 id="6.2">6.2 ???</h3>
<h3 id="6.3">6.3 ???</h3>
