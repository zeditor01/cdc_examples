[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Deploy Management Console and Access Server

This is a short, sweet and simple document, because deploying the CDC management tools is a simple task.

The diagram below represents the relationship between the CDC administration tools and the CDC Agents.

![cdcarch](/images/cdc/CDC_architecture.jpg)

## Description of the 3 primary components available for CDC management

The primary administration tools for CDC Replication are the Management Condole GUI (Windows only) and the Access Server (Windows or Linux).

***Management Console Graphical User Interface***. 

The management console is a very well designed user interface to enable the user 
to manage the entire lifecycle of CDC replication.
1. Define connections to CDC Capture and Apply Agents
2. Define subscriptions from CDC sources to CDC targets
3. Start and Stop subscriptions
4. Monitor subscriptions at a detailed level
5. Define alert conditions and thresholds
6. etc...

***Access Server*** is the access control point, where authentication and authorisation requirements can be enforced.

* The Access Server can be deployed as an independent server, in which case the Management Console connects to it via TCPIP.
* An embedded Access Server can be deployed inside the Windows Management Console, to provide a simpler deployment model for the tools.

***CHCCLP Scripting interface***

A scripting interface is available, which is useful for automation and promotion scenarios, where a task needs to be done using scripts that 
have been previously developed and tested. CHCCLP scripting interfaces are available in 3 places

1. At a command line interface where the Management Console has been deployed.
2. At a command line interface where the Access Server has been deployed.
3. On z/OS, using either the USS command line, or JCL jobs.


## Installing Access Server.

Installation of the CDC Access Server is a simple setup.exe style installer for Windows, or a tar-based installer for Linux. 
You need to make some fundamental security decisions before you install Access Server, as discussed below.

Aside from confirming Windows basics like installation path, program group and so forth, the major installation decisions you need to make are as follows.

Run the installer to see the installation dialog

![instas01](/images/instas01.JPG)

After responding to all the Path and License questions, you will be asked whether you wish to use an LDAP server for authentication only, or authentication and authorisation. The [securing CDC](https://github.com/zeditor01/cdc_examples/blob/main/documents/securing_cdc.md) document covers the LDAP choices in more detail.

In this example, we choose not to use an LDAP server for anything, which will mean that userids and passwords for CDC administration users are stored (in encrypted files) on the Access Server itself. That's probably ok for initial functional testing in a development environment, but a secure productin deployment will warrant a closer look at the security options.

![instas02](/images/instas02.JPG)

Access Server listens on a TCPIP port. The default port is 10101, which can be changed at installation time here.

If you deploy the CDC Management Console and Access Server in a production environment, you will likely want all TCPIP connections to be encrypted. Encryption can be specified at a later time, and is reviewed in the [securing CDC](https://github.com/zeditor01/cdc_examples/blob/main/documents/securing_cdc.md) document.

![instas03](/images/instas03.JPG)

Next up, you need to define the root CDC administration user. If you are not using LDAP, you must enter a userid and a password here. This a userid defined withn the Access Server, and it is totally independent of the authentcation control of the operating system where Access Server is installed.

In a production environment, you would likely be using an LDAP server, and the root CDC administration user would be defined with a CHCCLP command 
as covered in the [securing CDC](https://github.com/zeditor01/cdc_examples/blob/main/documents/securing_cdc.md) document.

![instas04](/images/instas04.JPG)

After running the installer, open the Windows Services application, and observe that an auto-start Windows Service has been established, so that the Access Server is always available on demand.

![instas05](/images/instas05.JPG)


## Installing Management Console.

Installation of the CDC Management Console is a simple setup.exe style installer for Windows.
Aside from confirming Windows basics like installation path, program group and so forth, the only decision you need to make is whether 
you wish to install and embedded access server, or connect to an independent access server.

Run the installer to see the installation dialog

![instmc01](/images/instmc01.JPG)

After responding to all the Path and License questions, you will be asked whether you wish to install and embedded access server, or connect to an independent access server.

In this example, we choose not to install the embedded access server, because we will connect to the access server we just deployed.

***Note*** A Management Console installation with an embedded access server cannot also connect to independent access servers. 

![instmc02](/images/instmc02.JPG)

After the installation completes, start the Management Console. You will be prompted to provide the TCPIP hostname and port of the access server, plus a userid and password that is registered at the access server (such as the root CDC administration user that we just defined).

![instmc03](/images/instmc03.JPG)

Now you can view the 3 main tabs of the Management Console. From right to left

* The Access Manager Tab is where we define connections to CDC capture and apply agents
* The Configuration Tab is where we define CDC subscriptions
* The monitoring Tab is where we operate and monitor CDC subscriptions

![instmc04](/images/instmc04.JPG)

All Done !



