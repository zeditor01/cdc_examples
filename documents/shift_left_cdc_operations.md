[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

# Shift-Left CDC Operations

This section addresses the practice of minimising or eliminating CDC operational responsibility from the target platform (typically Linux) 
and establishing all the CDC operational responsibilities on the target side (typically z/OS)

## Contents

<ul class="toc_list">
<li><a href="#1.0">1. Shift-Left Concepts</a>   
<li><a href="#2.0">2. Shift-Left Operations</a>   
<li><a href="#3.0">3. The Benefits of deploying CDC agents as zCX Containers</a>
    
<ul>  
  <li><a href="#3.1">3.1 Remote Capture for Db2 z/OS</a></li>
  <li><a href="#3.2">3.2 Remote Capture for VSAM</a></li>
</ul>  
  
<li><a href="#4.0">4. Summary and Recommendations</a> 
</ul>

<br><hr>
  
<h2 id="1.0">1. Shift-Left Concepts</h2>  

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

  
<br><hr>
  
<h2 id="2.0">2. Shift-Left Operations</h2> 
  

Separating a CDC Apply agent from a target database has always been easy to do between LUW platforms, because 
most target databases are based on a client-server deployment model, and CDC Apply only needs to connect to a database client.
  
However, when the source platform is z/OS, and the CDC Apply agent is only available for linux or windows, a shift-left configuration 
doesn't work.
  
Software Containers provide an easy solution for the common CDC use cases, as follows.
* The source database is Db2 z/OS (or IMS or VSAM on z/OS)
* The target "database" is Kafka on intel
* CDC Apply for Kafka is available for linux on intel, and also for linux on s390
* The CDC Apply for Kafka on s390 can be packaged as a docker container, which can run inside z/OS
* z/OS Container Extensions is a feature of z/OS V2.4 or later, providing a docker runtime environment for z/OS, with easy integration as a z/OS started task allowing z/OS platform capabilities to be inherited by the container. (z/OS operations, z/OS scheduling, z/OS system automation etc...)
  
![shift_l](/images/shift_l.png)

***Note*** z/OS Container extensions does require a software license for "Container Hosting Foundation (5655-HZ1).

<br><hr>

<h2 id="2.0">3. The Benefits of deploying CDC agents as zCX Containers</h2> 


The benefits listed in this section would apply to most z/OS centric sites, but the extent of the benefit will depend on the operational situation at a given site.
•	Some of the benefits listed in this section would apply to any container environment.
•	Some of the benefits listed in this section are specific to zCX in z/OS.
•	Some of the benefits listed in this section would only apply to sites who have a z/OS centric operational strengths.

2.1 Simpler, Faster Deployment.
Software containers are designed for point and shoot provisioning. If you can package your software into a container it will easier the deploy that running through a typical install and configure process for installing software into an operating system.
This is a benefit that you will realise for software containers on any platform. z/OS doesn’t miss out.

2.2 A single operations team to run the entire CDC solution.
The author of this document has worked with many organisations who implement data replication solutions between mainframe and midrange platforms like Linux. It is the norm that
1.	z/OS and midrange systems are managed by different operation teams
2.	z/OS and midrange operations team will use different tools and different procedures.
3.	Even if there is complete goodwill and commitment from both teams to work together there will be difficulties in managing a service that spans two operations teams.
One anonymous example is that it was designated that the mainframe team had operational responsibility for the CDC service, but they were not allowed to have read access to the CDC log directory on the CDC for Linux Apply Server. As a generalisation with CDC, if a simple restart doesn’t fix a problem, then the critical problem determination information will probably reside in the apply log file on the target side. The consequence of this decision is elongated restart times.


![opsteams](/images/opsteams.png)

Of course, if the Apply agent runs as a container within zCX, then the mainframe operations team will be able to access the Apply log themselves without queueing to speak with a help desk that fronts the Linux operations team.
 
2.3 A Better Physical Recovery Solution.
Any given group of CDC subscriptions can only be operated by one capture and one apply agent at once. In fact, CDC has serialization checks to ensure you don’t accidentally breach this data integrity matter. A consequence of this architectural model is that high availability configurations for CDC are based on the cluster failover model.
There are several OS clustering solutions on the market for Linux. But none of them come close to the speed and sophistication of system automation for restarting address spaces elsewhere in a z/OS parallel sysplex. The zCX address space can be configured with a DVIPA so that it can be automatically restarted on another LPAR in the event of a failure. This puts CDC Apply agents on the same level as z/OS started tasks like Classic CDC for IMS.

![zcxfailover](/images/zcxfailover.png)

2.4 Faster, Lower Risk Software Upgrades.
If you deploy CDC for Linux on a Linux Server, then a software upgrade will require the service to be stopped, a software upgrade to be applied (a few minutes) and a degree of risk the the upgraded software may have problems and require a fallback procedure.
When CDC is deployed as a software container, it can be deployed and tested inside the same zCX address space as the live service. Once testing is complete, you can stop the old container, attach the shared volume with the CDC instance information to the new container, and start the subscriptions over there. If problems occur, just reverse the process to fallback to the old container.


![zcxdisks](/images/zcxdisks.png)

2.5 Inherit z/OS Qualities of Service.
z/OS qualities of service are exceedingly high. (reliability, robustness, performance, automated local failover, GDPS disaster recovery etc… etc…)
Deploying CDC inside z/OS as a docker contain is an easy way to achieve z/OS qualities of service for very little effort. The main effort will be to establish zCX as another well-managed z/OS started task, with all the standard z/OS operational practices.

2.5 Inherit z/OS Security Strengths.
Operating CDC Apply engines inside zCX places them inside the IBM Z protected shell. This includes a range of heightened security provisions including
•	Pervasive encryption of all datasets
•	In-memory networks ( hipersockets ) that can’t be snooped on
•	EAL5 certified LPAR security
•	Etc…


3. z/OS Container Extensions (zCX).
z/OS Container Extensions, is essentially a framework for supporting docker within z/OS. If you know docker on Linux, Unix, Windows or Mac, then you know docker on z/OS.
The primary scope of this document is deploying CDC inside zCX. If you want to read more about zCX then please review some other source of zCX information, including the following Redbooks.

Getting started with z/OS Container Extensions and Docker
https://www.redbooks.ibm.com/abstracts/sg248457.html

IBM z/OS Container Extensions (zCX) Use Cases
https://www.redbooks.ibm.com/abstracts/sg248471.html

3.1 zCX Concepts.
zCX Support docker containers. Docker containers are more efficient than virtual machines in that rather than having multiple instances of the guest operating system, you only have one OS kernel (linux based) which is referenced by all the running docker containers. It is also operationally simpler in that the OS kernel is largely hidden from the containers, and you only need to deal with the containerised application.


![zcxconcepts](/images/zcxconcepts.png)

When Docker containers are deployed inside a zCX address space, they are tightly integrated with the z/OS software and IBM Z hardware environment. For example
•	They use a Dynamic Virtual IP Address for communications with other address space and with the outside word. 
•	TCPIP connectivity inside z/OS with other z/OS subsystems can use in-memory links (hipersockets).
•	System Automation for zCX Address Space(s) can automatically restart zCX services on different LPARs, and retain the same TPCIP and Disk connections.
•	z/OS storage subsystems can provide Hyperswap for local failovers and GDPS for geographically remote failovers.

![zcxtcpip](/images/zcxtcpip.png)

3.2 zCX Setup.
Chapter 4 of "Getting started with z/OS Container Extensions and Docker" is the best guide to installing and confguring zCX. I am not going to repeat that information here. Please review the redbook to learn how to setup zCX.
https://www.redbooks.ibm.com/abstracts/sg248457.html
Table of contents
•	Chapter 1. Introduction
•	Chapter 2. z/OS Container Extensions planning
•	Chapter 3. Security - Overview
•	Chapter 4. Provisioning and managing your first z/OS Container
•	Chapter 5. Your first running Docker container in zCX
•	Chapter 6. Private registry implementation
•	Chapter 7. Operation
•	Chapter 8. Integrating container applications with other processes on z/OS
•	Chapter 9. zCX user administration
•	Chapter 10. Persistent data
•	Chapter 11. Swarm on zCX

Chapter 5 addresses deploying docker containers within zCX.
Chapter 7 addresses the operation of docker containers within zCX.



4. Worked Example: CDC for Kafka in zCX.
Having explained why you might want to consider deploying CDC agents inside zCX, the remainder of this document is a simple worked example.
The scenario implemented is to capture from IMS and apply to Kafka. Classic CDC for IMS is the capture agent. CDC for Kafka for linux on z390 is the Apply agent.

![shift_l](/images/shift_l.png)

Note: we cannot containerise linux for Intel or linux for Power software for zCX. We must use software that is compiled for z390 because that is the runtime hardware platform. This is not a great restriction as many linux software products are compiled for s390 as well as other platforms.

4.1 Creating the CDC Docker Container.
There are a great many docker containers that are pre-built for s390, and ready to deploy in zCX. Pop over to docker hub and browse the containers that you can pull on demand. Filter by s390 platform to see which of them are available for Z.
https://hub.docker.com/
CDC software is not actually shipped as a Docker container. That’s not a problem because you can build your own container with a “Dockerfile”. A dockerfile is a set of instructions to build a container from a software image. 
You can download a dockerfile for CDC for Kafka at the following IBM github repository.
https://github.ibm.com/replication/cdc-luw-docker/tree/master/cdckafka
That dockerfile was actually written for CDC for Kafka on Intel. However, if you change the name of the CDC installer binary to the s390 version, the dockerfile runs perfectly in zCX ! That’s an experience which demonstrates that docker is docker even on Z.
The dockerfile source code is pasted into the Appendex for review. The only difference to deploy to zCX is to reference setup-iidr-11.4.0.0-5001-linux-s390.bin. But even that doesn’t require a dockerfile change, because the installer image is named as an input parameter to the dockerbuild command.

Step 1 : Assume ZCX environment is Established
•	My ZCX is running at IP Address 192.168.1.220
•	My ZCX is listening on port 8022
•	My zCX has connectivity to the internet, needed to pull packages from repositories

 
Step 2 : Verify Docker with hello world.
ssh into the zCX shell and issue docker run hello-world

![zcx01](/images/zcx01.png)


Step 3 : Create a directory to gather everything you need to run the dockerfile
mkdir /home/admin/cdckafka
cd /home/admin/cdckafka

This is where you will gather the artefacts to build the docker container

Step 4 : Gather the following 4 files
•	The Dockerfile
•	The response file for a silent install of CDC for Kafka for Linux on Z
•	The kafkaproducer.properties file ( to save editing it after the install )
•	The installer binary.

![zcx02](/images/zcx02.png)


Step 5 : Invoke the Docker build process
Run the following command from /home/admin/cdckafka
docker build --build-arg CDCINSF=setup-iidr-11.4.0.0-5001-linux-s390.bin -t zcdckafka .

![dockerbuild01](/images/dockerbuild01.png)

and

![dockerbuild02](/images/dockerbuild02.png)

and

![dockerbuild03](/images/dockerbuild03.png)


That’s it. The docker build runs in a few minutes on my ZD&T environment. It should run in seconds on a real Z system. I attach screenshots of the docker build output in the appendix.


4.2 Operating the CDC Docker Container.  
Before we start the container, we want to add a docker volume. This is not strictly necessary for CDC for Kafka since the container is persisted. However by placing the instance metadata (bookmark etc… ) on a shared docker volume, you have flexibility for things like fast low-risk software upgrades.
Standard docker commands to create a docker volume.


![zcx03](/images/zcx03.png)


Now you can start the container, referencing the shared volume and it’s mountpoint within the container. You also need to surface the listener port 11701 outside the zCX DVIPA.
docker run -idt -v cdcvol:/cdcinstance/ -p 11701:11701 zcdckafka 

Remember the difference between docker run and docker start
•	Docker run will create an instance and start it.
•	Docker start will restart a previously created container instance, with all the metadata that you want to keep.

After you start the container, run docker ps -a to discover the container ID.


![zcx04](/images/zcx04.png)

Now connect to that container and start a bash shell
docker exec -it 733de0daa126 /bin/bash

![zcx05](/images/zcx05.png)

4.3 Configuring the CDC instance.  
From here on in, it’s no different from running CDC in Linux. Create an instance as normal.
From /opt/cdckafka/bin
issue
./dmconfigurets



![zcx06](/images/zcx06.png)


And then start the instance
cd /opt/cdckafka/bin
./dmts64 -I zcxcdckafka &


![zcx07](/images/zcx07.png)


Now you can point all your normal CDC Administration Tools ( Management Console, Access Server, CHCCLP for linux, CHCCLP for z/OS ) at the CDC instance on 192.168.1.220:11701

![zcx08](/images/zcx08.png)

Creating and Operating CDC subscriptions via CDC for Kafka in zCX is no different from any other supported CDC platform.


![zcx09](/images/zcx09.png)

We provide support (i.e. accept/work support tickets) for our CDC software, whether it is running on bare metal, VMs, or in a container.

We  provide our customers with a sample set of instructions on how to create docker images for our CDC software. The customer is free to use those sample instructions, or create their own docker images as they choose. Our support will be limited to our CDC software that runs in the container or on the VM


4.4 Integration with z/OS Operations.  
The steps we went through to get into the docker container within zCX first time around were not as simple as you would want for an agile Devops environment. No problem : we can feed commands into the docker container from outside.
For example, from the zCX shell you can invoke the command to start the CDC instance as follows, without needing to open a bash shell inside the CDC container.
docker exec -it <containerId> /opt/cdckafka/bin/dbts64 -I cdc5569

And if you want to wrap it in standard JCL, just place your docker commands in a file and invoke them from BPXBATCH
//DEFACL   EXEC PGM=IKJEFT01                        
//SYSPRINT DD SYSOUT=*                              
//SYSUDUMP DD SYSOUT=*                              
//SYSTSPRT DD SYSOUT=*                              
//STDOUT DD SYSOUT=*                                
//STDERR DD SYSOUT=*                                
//SYSTSIN DD *                                      
    BPXBATCH SH +                                   
    ssh admin@<zcxIPaddr> -p 8022 < zcxCmds.sh 


Easy As …

Running CDC agents inside zCX would be a great option if you seek the qualities of service provided by z/OS, have some zIIP processor capacity, and want to take advantage of z/OS operational practices and platform capabilities.

                                              
Dockerfile
```
FROM registry.access.redhat.com/ubi7/ubi:latest

ARG CDCINSF

# check if CDCINSF build argument is set and exit if not

RUN if [ -z $CDCINSF ] ; then printf "\033[32m" ; echo -e "\n\nMandatory argument CDCINSF not set! \n\nARGUMENT: \n CDCINSF - cdc installer name eg. setup-iidr-11.4.0.2-5512-linux-x86.bin \n To be provided from the command line (docker build --build-arg CDCINSF=setup-iidr-11.4.0.2-5512-linux-x86.bin)\n\n"; printf "\033[39m" ; exit 1 ; else : ; fi



ARG RFILE=cdckafka.rfile

RUN printf "\033[32m" ; echo -e "\n\nARGUMENT:\n RFILE - CDC installer response file name\n\n" ; printf "\033[39m"



ARG EXP_PORT=11701

RUN printf "\033[32m" ; echo -e "\n\nARGUMENT:\n CDC instance port to be exposed. By default 11701\n\n" ; printf "\033[39m"



ARG IMAGE_DISPLAY_NAME="Docker image with CDC Replication Engine"

ARG IMAGE_DESCRIPTION="This image runs IBM InfoSphere Data Replication for Kafka software"



# required by RedHat certification / UBI label overrides

# see https://github.com/projectatomic/ContainerApplicationGenericLabels/blob/master/vendor/redhat/labels.md

LABEL name="cdcforkafka" \

summary="${IMAGE_DISPLAY_NAME}" \

description="${IMAGE_DESCRIPTION}" \

io.k8s.display-name="${IMAGE_DISPLAY_NAME}" \

io.k8s.description="${IMAGE_DESCRIPTION}"



# location of installation and instance directories 

ENV CDC_HOME=/opt/cdckafka

ENV CDC_INSTANCE_PATH=/cdcinstance



# upgrade image and install required packages 

RUN yum -y upgrade

RUN yum -y install unzip wget



# Copying installer and response file

COPY $CDCINSF $RFILE /tmp/



USER root

RUN mkdir -p $CDC_HOME $CDC_INSTANCE_PATH && \

chmod +x /tmp/$CDCINSF 



RUN groupadd -g 1100 cdc \

&& useradd -u 1100 -g 1100 cdc



RUN printf "\033[32m" ; echo -e "\n\nInstalling CDC using silentmode and response file\nInstance directory $CDC_INSTANCE_PATH should be stored on the persistent volume\n\n" ; printf "\033[39m"



WORKDIR /tmp

ADD kafkaproducer.properties /tmp



RUN sed -i -e "s|###CDC_INSTANCE_PATH###|$CDC_INSTANCE_PATH|g" $RFILE && \

sed -i -e "s|###CDC_HOME###|$CDC_HOME|g" $RFILE && \

./$CDCINSF -i silent -f $RFILE && \

cp /tmp/kafkaproducer.properties $CDC_HOME/conf && \

rm -rf $CDCINSF /tmp/$RFILE && \

chown -R cdc:cdc $CDC_HOME $CDC_INSTANCE_PATH



USER cdc

CMD tail -f /dev/null



EXPOSE $EXP_PORT

```

## Summary and Recommendations





dockerfile and zCX process



Observations about CDC for Kafka in zCX. ( same as )

Operations and System Automation

