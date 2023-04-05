[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Deploy Management Console and Access Server

This is a short, sweet and simple document, because deploying the CDC management tools is a simple task.

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

Installation of the CDC Access Server is a simple setup.exe style installer for Windows, or a tar-based installer for Linux

Aside from confirming Windows basics like installation path, program group and so forth, the major installation decisions you need to make are as follows.

Run the installer to see the installation dialog

![instas01](/images/instas01.JPG)

xx

![instas02](/images/instas02.JPG)

xx

![instas03](/images/instas03.JPG)

xx

![instas04](/images/instas04.JPG)

Open the Windows Services application, and observe that an auto-start Windows Service has been established, so that the Access Server is always available on demand.

![instas05](/images/instas05.JPG)


## Installing Management Console.

Installation of the CDC Management Console is a simple setup.exe style installer for Windows.

Aside from confirming Windows basics like installation path, program group and so forth, the major installation decisions you need to make are as follows.





