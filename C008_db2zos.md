<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>ZEDITOR</title> 
<link rel="stylesheet" href="/recipes/styles.css">
<link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Lato">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head> 
<style>
html,body,h1,h2,h3,h4 {font-family:"Lato", sans-serif}
.mySlides {display:none}
.w3-tag, .fa {cursor:pointer}
.w3-tag {height:15px;width:15px;padding:0;margin-top:6px}

.greendot {
  height: 12px;
  width: 12px;
  background-color: #7CFC00;
  border-radius: 50%;
  display: inline-block;
}

.greydot {
  height: 12px;
  width: 12px;
  background-color: #bbb;
  border-radius: 50%;
  display: inline-block;
}

spandarkblue {
  background-color: #0000ff;
  color: white;
}

spanlightblue {
  background-color: #00ffff;
  color: black;
}

</style>
<body style="margin:0px;"> 

<!-- Persistent Site Links (sit on top) -->
<div class="w3-top">
  <div class="w3-row w3-large w3-blue">
	<div class="w3-col s3">
      <a href="/recipes/index.html" class="w3-button w3-block"><img src="/recipes/images/ZICON.png" height=25px></a>
    </div>
    <div class="w3-col s3">
      <a href="/recipes/index.html" class="w3-button w3-block">Home</a>
    </div>
    <div class="w3-col s3">
      <a href="/recipes/index.html#zones" class="w3-button w3-block">Technical Zones</a>
    </div>
    <div class="w3-col s3">
      <a href="/recipes/index.html#about" class="w3-button w3-block">About</a>
    </div>
  </div>
</div>



<!-- Start of Page Content -->

<!-- Common Header for CDC Papers -->

<div class="w3-content w3-light-grey" style="max-width:1100px;margin-top:80px;margin-bottom:80px">

<h2>CDC Replication Worked Examples</h2>

<!-- A Grid with hyperlinks to the CDC papers   -->
  <div class="w3-row-padding">

    <div class="w3-third">
      <ul class="w3-ul w3-border w3-left w3-hover-shadow" style="width:100%">
        <li class="w3-black w3-large w3-padding-4">Using CDC</li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu1.html"> Environment for CDC Worked Examples.</a></li> 
		<li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu2.html"> Creating and Operating CDC Subscriptions.</a></li> 
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu3.html"> Devops Options for CDC.</a></li> 
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu4.html"> CHCCLP Scripting.</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu5.html"> Security for CDC (LDAP and TLS).</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu6.html"> Container Deployment.</a></li>
		<li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcu7.html"> Monitoring and Managing outwith the Windows MC.</a></li>
      </ul>
    </div>

    <div class="w3-third">
      <ul class="w3-ul w3-border w3-left w3-hover-shadow" style="width:100%">
        <li class="w3-black w3-large w3-padding-4">Deploying z/OS CDC Agents</li>
        <li class="w3-small"><span class="greendot"></span><a href="/recipes/docs/NA_CDC/cdcz1.html"> Setting up CDC for Db2 on z/OS.</a></li> 
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcz2.html"> Setting up Classic CDC for IMS.</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcz3.html"> Setting up Classic CDC for VSAM.</a></li> 
		<li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcz4.html"> Setting up CDC for Kafka in zCX.</a></li> 
      </ul>
    </div>
	
    <div class="w3-third">
      <ul class="w3-ul w3-border w3-left w3-hover-shadow" style="width:100%">
        <li class="w3-black w3-large w3-padding-4">Deploying Linux CDC Agents</li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcl1.html"> Setting up CDC for Db2 on Linux.</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcl2.html"> Setting up CDC for Kafka.</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcl3.html"> Setting up remote CDC Capture for Db2 z/OS.</a></li>
        <li class="w3-small"><span class="greydot"></span><a href="/recipes/docs/NA_CDC/cdcl4.html"> Setting up remote CDC Capture for VSAM.</a></li>
      </ul>
    </div>

  </div>
<!-- End Grid with hyperlinks to the CDC papers -->

<hr>

</div>

<!-- Page Contents Here -->

<div class="w3-content" style="max-width:1100px;margin-top:80px;margin-bottom:80px">

<h2>Setting up CDC for Db2 z/OS.</h2>

   <!-- Grid -->
  <div class="w3-row-padding">

    <div class="w3-third w3-margin-bottom" style="width:100%">
      <ul class="w3-ul w3-border w3-left w3-hover-shadow">
		<li>Author: Neale Armstrong</li>
		<li>Date Last Updated: February 2022</li>
      </ul>
    </div>

  </div>

<hr>

<div id="toc_container">

<p class="toc_title">Table of Contents</p>
<ul class="toc_list">
<li><a href="#abstract">Abstract</a>   
<li><a href="#1.0">1 Introduction to CDC for Db2/z/OS</a>
<ul>
  <li><a href="#1.1">1.1 Requirements to Replicate Db2 z/OS Data</a></li>
  <li><a href="#1.2">1.2 The CDC Started Task</a></li>
</ul>
<li><a href="#2.0">2. High Level Review of Implementation Steps</a>
<li><a href="#3.0">3. SMPE Installation of Code Libraries</a>
<li><a href="#4.0">4. Creating the CDC Instance</a>
<ul>
  <li><a href="#4.1">4.1 Edit the SCHCDATA config dataset members</a></li>
  <li><a href="#4.2">4.2 Create PAL ( Product Admin Log )</a></li>
  <li><a href="#4.3">4.3 Create cluster for metadata</a></li>
  <li><a href="#4.4">4.4 Bind Packages for metadata utility</a></li>  
  <li><a href="#4.5">4.5 Create Metadata tables in Db2</a></li>
  <li><a href="#4.6">4.6 Grant Package Execution</a></li>
  <li><a href="#4.7">4.7 Bind CDC Plan</a></li>
  <li><a href="#4.8">4.8 Create Log Cache ( Single Scrape )</a></li>  
</ul> 
<li><a href="#5.0">5. Configure the z/OS Environment</a>
<ul>
  <li><a href="#5.1">5.1 APF Authorised Load Libraries</a></li>
  <li><a href="#5.2">5.2 TCPIP Ports</a></li>
  <li><a href="#5.3">5.3 RACF Started Task ID</a></li>
  <li><a href="#5.4">5.4 Test Start the CDC Server</a></li>  
</ul>
<li><a href="#6.0">6. Configure the Db2 z/OS Environment</a>
<ul>
  <li><a href="#6.1">6.1 Alter Source Tables for full row logging</a></li>
  <li><a href="#6.2">6.2 Db2 configuration considerations</a></li>
</ul>
<li><a href="#7.0">7. Integrate with the wider CDC Landscape</a>
<ul>
  <li><a href="#7.1">7.1 Deploy CDC as a Started Task</a></li>
  <li><a href="#7.2">7.2 Connect from Management Console to Classic CDC Started Task</a></li>
  <li><a href="#7.3">7.3 Use CHCCLP Scripting</a></li>  
  <li><a href="#7.4">7.4 Conforming to site standards for cross-platform devops and security</a></li>
</ul> 
<li><a href="#App">Appendix</a>
</ul>
</div>

<hr>


<h2 id="abstract"> Abstract</h2>
<p>This document is a basic worked example of setting up CDC for Db2 z/OS.</p> 
<ul>
<li>It deals with the practical considerations for implementing Db2 z/OS as a CDC data source. 
<li>It's scope is limited to a "basic up and running guide", and is intended to be easy to follow (assuming a base of z/OS and Db2 z/OS practical experience).
<li>It does not attempt to cover all the product's features.
<li>It is categorically <b>not</b> a replacement for the  
<a href="https://www.ibm.com/docs/en/idr/11.4.0?topic=replication-infosphere-cdc-db2-zos">IBM CDC knowledge centre</a>, which is the comprehensive official product documentation.
</ul> 

<p>It is part of a series of documents providing practical worked examples and 
guidance for seting up CDC Replication between mainframe data sources and mid-range or Cloud targets.
The complete set of articles can be accessed using the links at the very top of this page</p> 

<br><hr>

<h2 id="1.0">1. Introduction to CDC for Db2 z/OS</h2>  

<p>IBM InfoSphere Data Replication for z/OS (aka CDC for Db2 z/OS) is a full CDC implementation providing both CDC Capture and CDC Apply functionality.</p>

<p>CDC Replication is a set of products that implement a common data replication architecture spanning 
a large number of diverse data sources and targets. The CDC common architecture is based upon replication of 
data that conforms to the relational model. Any CDC capture or apply agent that supports a non-relational data structure 
must perform whatever conversion work that is necessary to implement a mapping between that data structure and the 
relational model of data.</p> 

<p>Since Db2 z/OS is a relational database with robust, well-documented interfaces for accessing data and logs, the implementation of CDC for Db2 z/OS 
is very straightforward indeed, as this article will show.</p>

<h3 id="1.1">1.1 Requirements to Replicate Db2 z/OS Data</h3> 

<p>The core functionaility of any CDC Capture agent is to read the source database logs asynchronously, 
stage captured data (preferably in memory) until the Unit of Work is commited or rolled back, 
and then publish the committed changes over TCPIP sockets to a CDC Apply agent.</p> 

<p>Any supported version of Db2 z/OS is ready to go with CDC. The customisation of Db2 to support CDC is limited to:</p>
<ol>
<li>Creating a few CDC control tables insider Db2 z/OS
<li>Binding a plan and a few packages
<li>Granting the started task ID sufficient privileges to access the Db2 Catalog and Log Interface
<li>Altering source tables to enforce full row logging so that the log records contain enough information to support replication
<li>


<h3 id="1.2">1.2 The CDC Started Task</h3>
<p>The diagram below is a representation of the components within a CDC capture instance, a CDC Apply instance, and how they 
relate to external artefacts.</p>

<center><img src="/recipes/images/neale/cdc/CDC_architecture.jpg"  style="border:1px solid black; width:800px"></center> 


<p>The CDC services are summarised in the table below.</p>

 <table>
  <tr>
    <th width=300>Service</th>
    <th width=500>Function</th>
  </tr>
  <tr><td>Admin Agent</td>
    <td>Listens on a TCPIP port for requests from other CDC components and communicates with other services.</td>
  </tr> 
  <tr>
   <td>Admin API</td>
   <td>Processes commands and scripts. (operations, monitoring etc... )</td>
  </tr> 
   <tr>
   <td>Comm Layer</td>
   <td>handles TCPIP sockets between CDC Capture and CDC Apply servers.</td>
  </tr> 
  <tr>
   <td>Refresh Reader</td>
   <td>Reads Db2 tables for initialisation of subscriptions.</td>
  </tr> 
  <tr>
   <td>Log Reader</td>
   <td>Uses DB2 IFI to read Db2 logs.</td>
  </tr> 
  <tr>
   <td>Single Scrape</td>
   <td>Cache to ensure that logs are read only once if you have multiple subscriptions.</td>
  </tr> 
  <tr>
   <td>Source Transformation Engine</td>
   <td>Performs transformations that are configured to execute on the Capture server</td>
  </tr>  
  <tr>
   <td>Target Transformation Engine</td>
   <td>Performs transformations that are configured to execute on the Apply server</td>
  </tr> 
  <tr>
   <td>Apply Agent</td>
   <td>Writes data to target tables.</td>
  </tr> 
</table> 


<p>All the services, and their governing parameters are documented in the <a href="https://www.ibm.com/docs/en/idr/11.4.0?topic=zos-about-cdc-replication">knowledge centre</a>.

<br><hr>

<h2 id="2.0">2. High Level Review of Implementation Steps</h2>
<p>There are a lot of moving parts, and a lot of inter-related dependencies in setting up Classic CDC for VSAM. 
It is helpful to establish a structured overview of the main installation and configuration activities before 
diving into the technical details of very nut and bolt. 
This paper identifies five separate stages of implementation</p>

	<ul> 
	<li>SMPE Installation of Code Libraries
	<li>Creating the Customised CDC Instance
	<li>Configure the z/OS Environment
	<li>Configure the Db2 z/OS Environment
	<li>Integrate with the wider CDC Landscape
	</ul>

<br><hr>

<h2 id="3.0">3. SMPE Installation of Code Libraries</h2>
<p>SMPE installation is a well documented, standardised process that every systems programming shop manages with 
their own standards. It is outside the scope of this paper, aside from noting that it is a pre-requisite to the 
followng stages.</p>
<p>Once the SMPE installation is complete, the following target libraries will exist under the chosen high level qualifier. (CDCD in this example).</p>

 <table>
  <tr><td width=200><b>Library</b></td><td width=500><b>Contents</b></td></tr> 
  <tr><td>CDCD.SCHCASM</td><td>?</td></tr> 
  <tr><td>CDCD.SCHCC</td><td>?</td></tr>  
  <tr><td>CDCD.SCHCCNTL</td><td>Sample JCL</td></tr>  
  <tr><td>CDCD.SCHCCOB</td><td>?</td></tr>  
  <tr><td>CDCD.SCHCDATA</td><td>Configuration library</td></tr> 
  <tr><td>CDCD.SCHCDBRM</td><td>DBRM Library</td></tr>  
  <tr><td>CDCD.SCHCH</td><td></td></tr>  
  <tr><td>CDCD.SCHCLOAD</td><td>Load Modules</td></tr>  
  <tr><td>CDCD.SCHCMAC</td><td>Macros</td></tr>  
  <tr><td>CDCD.SCHCNOTC</td><td>?</td></tr>  
  <tr><td>CDCD.SCHCTTL</td><td>?</td></tr>  
</table> 




<br><hr>
	
<h2 id="4.0">4. Creating the CDC Instance</h2>
<p>The installed product code can now be used to support one or more instances of CDC for Db2 z/OS. 
This worked example will create a single instance, under the instance high level qualifier of CDCD (in this example).</p>

<h3 id="4.1">4.1 Edit the SCHCDATA config dataset members</h3> 
<p>With CDC for Db2 z/OS an instance of CDC is defined by creating a set of members in the SCHCDATA dataset that define the identity of the instance.</p>
<p>Inside CDCD.SCHCDATA you will find four 'XX' template members (CHCCFGXX. CHCCMMXX, CHCDBMXX, CDCLDRXX and CHCUCSXX). Make copies of these members into a different 
suffix (65) which will be the identity of the CDC for z/OS instance that we are going to create.</p>


<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>CHCCFGXX                                                           </code>  
<code>CHCCMMXX                                                           </code>
<code>CHCDBMXX                                                           </code>
<code>CHCLDRXX                                                           </code>
<code>CHCUCSXX                                                           </code>
<code>CHCCFG65                                                           </code>
<code>CHCCMM65                                                           </code>
<code>CHCDBM65                                                           </code>
<code>CHCLDR65                                                           </code>
<code>CHCUCS65                                                           </code>
<code>CHCMTDIN                                                           </code>
<code>CHCTMZON                                                           </code>
<code>CHCUEIMP                                                           </code>
<code>CHCUTIL                                                            </code>                                                           
</pre>
</div>


<p><b>CDCD.CHCCFG65</b> contains the general configuration statements for this instance. The defaults are all fine for a basic first setup.</p>
<center><img src="/recipes/images/neale/cdc/chccfg65.PNG" style="width:800px"></center>

<p><b>CDCD.CHCCMM65</b> contains TCPIP information. Idefined the listener port as 6789.</p>
<center><img src="/recipes/images/neale/cdc/chccmm65.PNG" style="width:800px"></center>

<p><b>CDCD.CHCDBM65</b> contains Db2 z/OS connection paramaters. I identified my Db2 z/OS V12 system by providing SSID. 
I also provided the configuration values for a basic log cache which helps performance when you have multiple subscriptions.</p>
<center><img src="/recipes/images/neale/cdc/chcdbm65.PNG" style="width:800px"></center>

<p><b>CDCD.CHCLDR65</b> contains data loader configuration statements. The defaults are all fine for a basic first setup.</p>
<center><img src="/recipes/images/neale/cdc/chcldr65.PNG" style="width:800px"></center>

<p><b>CDCD.CHCUCS65</b> contains code page configuration parameters. The defaults were fine for my system.</p>
<center><img src="/recipes/images/neale/cdc/chcucs65.PNG" style="width:800px"></center>

<p>After you have got a basic CDC server up and running, you will want to review all the parameter settings in the context of your scenario. The documentation describes all the 
paramaters that can be coded (and their default values) in the <a href="https://www.ibm.com/docs/en/idr/11.4.0?topic=ccdz-modifying-cdc-db2-zos-configuration-control-data-set">knowledge centre</a>.

 

<h3 id="4.2">4.2 Create PAL ( Product Admin Log )</h3> 

<p>Customize the job CDCD.SCHCCNTL(CDCDFPAL) to create a VSAM cluster for the CDC Replication product administration log (PAL).</p>


<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>  DEFINE -                                                              </code> 
<code>    CLUSTER -                                                           </code> 
<code>     (NAME(CDCD.PAL)                                                  - </code> 
<code>      VOL(USER0A)                                                     - </code> 
<code>      SHAREOPTIONS(2 3) -                                               </code> 
<code>      CYL(1 1)  -                                                       </code> 
<code>      RECSZ(36 1024) -                                                  </code> 
<code>      INDEXED -                                                         </code> 
<code>      NOREUSE -                                                         </code> 
<code>      KEYS(32 0)) -                                                     </code> 
<code>    DATA -                                                              </code> 
<code>     (NAME(CDCD.PAL.DATA))                                            - </code> 
<code>    INDEX -                                                             </code> 
<code>     (NAME(CDCD.PAL.INDEX))                                             </code> 
<code>                                                                        </code> 
<code>  REPRO -                                                               </code> 
<code>    INFILE(CHCPALDT) -                                                  </code> 
<code>    OUTDATASET(CDCD.PAL)                                                </code> 
</pre>
</div>


<h3 id="4.3">4.3 Create cluster for metadata</h3> 

<p>Customize the job CDCD.SCHCCNTL(CDCDFMTD) to create the CDC Replication metadata VSAM cluster.</p>

<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>  DEFINE -                                                              </code> 
<code>    CLUSTER -                                                           </code> 
<code>     (NAME(CDCD.META)                                                 - </code> 
<code>      VOL(USER0A)                                                     - </code> 
<code>      SHAREOPTIONS(3 3) -                                               </code> 
<code>      CYL(20 20)  -                                                     </code> 
<code>      RECSZ(512 2048) -                                                 </code>  
<code>      CISZ(4096) -                                                      </code> 
<code>      INDEXED -                                                         </code> 
<code>      NOREUSE -                                                         </code> 
<code>      KEYS(128 16)) -                                                   </code> 
<code>    DATA -                                                              </code> 
<code>     (NAME(CDCD.META.DATA))                                           - </code> 
<code>    INDEX -                                                             </code> 
<code>     (NAME(CDCD.META.INDEX))                                            </code> 
<code>                                                                        </code> 
<code>  REPRO -                                                               </code> 
<code>    INFILE(VSAMINIT) -                                                  </code> 
<code>    OUTDATASET(CDCD.META)                                               </code> 
</pre>
</div>


<h3 id="4.4">4.4 Bind Packages for metadata utility</h3> 

<p>Customize the job CDCD.SCHCCNTL(CDCMDMDB) to bind the packages and plan for the CHCMDMUT (metadata install and upgrade utility) program.</p> 

<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>//SYSTSIN  DD *                                                         </code> 
<code>  DSN SYSTEM(DBCG)                                                      </code> 
<code>  BIND -                                                                </code> 
<code>    PACKAGE(CHCPK165)                                                 - </code> 
<code>    MEMBER(CHCMDMMN) -                                                  </code> 
<code>    ACTION(REPLACE) -                                                   </code> 
<code>    CURRENTDATA(NO) -                                                   </code> 
<code>    ENCODING(UNICODE) -                                                 </code> 
<code>    OWNER(IBMUSER)                                                    - </code> 
<code>    QUALIFIER(IBMUSER)                                                  </code> 
<code>  BIND -                                                                </code> 
<code>    PACKAGE(CHCPK165)                                                 - </code> 
<code>    MEMBER(CHCMDMDB) -                                                  </code> 
<code>    ACTION(REPLACE) -                                                   </code> 
<code>    CURRENTDATA(NO) -                                                   </code> 
<code>    ENCODING(UNICODE) -                                                 </code> 
<code>    OWNER(IBMUSER)                                                    - </code> 
<code>    QUALIFIER(IBMUSER)                                                  </code> 
<code>  BIND -                                                                </code> 
<code>    PACKAGE(CHCPK265)                                                 - </code> 
<code>    MEMBER(CHCDB2DL) -                                                  </code> 
<code>    ACTION(REPLACE) -                                                   </code> 
<code>    CURRENTDATA(NO) -                                                   </code> 
<code>    ENCODING(UNICODE) -                                                 </code> 
<code>    OWNER(IBMUSER)                                                    - </code> 
<code>    QUALIFIER(IBMUSER)                                                  </code> 
<code>  BIND -                                                                </code> 
<code>    PLAN(CHCMDM65)                                                    - </code> 
<code>    PKLIST(CHCPK165.CHCMDMMN -                                          </code> 
<code>           CHCPK165.CHCMDMDB -                                          </code> 
<code>           CHCPK265.CHCDB2DL -                                          </code> 
<code>          ) -                                                           </code> 
<code>    ACTION(REPLACE) RETAIN -                                            </code> 
<code>    CACHESIZE(256) -                                                    </code> 
<code>    ISOLATION(CS) -                                                     </code> 
<code>    OWNER(IBMUSER)                                                    - </code> 
<code>    RELEASE(COMMIT)                                                     </code> 
<code>  END                                                                   </code> 
</pre>
</div>                                           

		   

<h3 id="4.5">4.5 Create Metadata tables in Db2</h3> 

<p>Customize the job CDCD.SCHCCNTL(CHCMDMUT) to create or upgrade the CDC Replication metadata tables. This table defines the identity of 
a CDC instance by identifying all the DB2 z/OS artefacts that the instance should refer to. The SYSIN data for this job is commented very 
well, and the values used in this worked example are shown below.</p> 

<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>//SYSIN    DD  *                                                       </code> 
<code>*Lines with an '*' in column 1 are treated as comments.                </code> 
<code>*                                                                      </code> 
<code>* Subsystem or data sharing group name of the                          </code> 
<code>* DB2 system which holds (or will hold) the metadata.                  </code> 
<code>SUBSYS=DBCG                                                            </code> 
<code>* The plan suffix used when the plan CHCMDMxx was bound.               </code> 
<code>PLANSFX=65                                                             </code> 
<code>* The name of the DB2 storage group which should be used               </code> 
<code>* to create any metadata objects. This storage group                   </code> 
<code>* will be created if it does not exist.                                </code> 
<code>STOGROUP=SYSDEFLT                                                      </code> 
<code>* The list of volume names to be used to create the                    </code> 
<code>* storage group if it does not exist. Must be specified                </code> 
<code>* if the storage group does not exist.                                 </code> 
<code>*OLUMES=<CHCVOLSER>                                                    </code> 
<code>* The name of the catalog to use in creating the                       </code> 
<code>* storage group if it does not exist. Must be specified                </code> 
<code>* if the storage group does not exist.                                 </code> 
<code>*ATLG=<CHCCatlg>                                                       </code> 
<code>* The name of the database which holds the metadata.                   </code> 
<code>* This database will be created if it does not exist.                  </code> 
<code>DBNAME=CHCDB                                                           </code> 
<code>* The name of the table space which will contain the majority          </code> 
<code>* of the metadata tables. This tablespace should be a Unicode          </code> 
<code>* table space with a page size of 4K. This table space will            </code> 
<code>* be created if it does not exist.                                     </code> 
<code>TBSP01=CHCTBSP1                                                        </code> 
<code>* The name of the table space which will contain metadata tables       </code> 
<code>* which require a 32K page size. This tablespace should be a           </code> 
<code>* Unicode table space with a page size of 32K. This table space        </code> 
<code>* will be created if it does not exist.                                </code> 
<code>TBSP02=CHCTBSP2                                                        </code> 
<code>* The buffer pool to be used when creating a new database,             </code> 
<code>* tablespaces and indexes requiring a 4k page size.                    </code> 
<code>* By default, bufferpool BP0 will be used.                             </code> 
<code>* If the database specified by DBNAME already exists then its          </code> 
<code>* buffer pool will be used instead.                                    </code> 
<code>BPOOL4K=BP0                                                            </code> 
<code>* The  buffer pool to be used when creating a new tablespace,          </code> 
<code>* and index that requires a 32k page size.                             </code> 
<code>* By default, bufferpool BP32K will be used.                           </code> 
<code>BPOOL32K=BP32K                                                         </code> 
<code>* The user name which owns the metadata.                               </code> 
<code>OWNER=IBMUSER                                                          </code> 
<code>* The SQLID this job should operate under. By default, this            </code> 
<code>* is the user ID this job is executing under.                          </code> 
<code>SQLID=IBMUSER                                                          </code> 
<code>* When set to Y, this option will cause all existing metadata          </code> 
<code>* tables to be dropped and a completely new set of metadata            </code> 
<code>* created. This option is intended to facilitate start over            </code> 
<code>* scenarios, e.g. if re-running a fresh install or if running          </code> 
<code>* an upgrade where metadata is not needed and will be recreated.       </code> 
<code>* It should be set to N otherwise.                                     </code> 
<code>* In general, it is not recommended to be set to Y for upgrade         </code> 
<code>* since all existing product metadata will be lost.                    </code> 
<code>DROPTBL=N                                                              </code> 
<code>* When set to Y, this option will cause all DB2 system                 </code> 
<code>* catalog tables indexes to be created. The storage group              </code> 
<code>* used for the indexes will be the storage group specified.            </code> 
<code>CRSYSIND=N                                                             </code> 
<code>* When set to Y, this option will set DATA CAPTURE CHANGES             </code> 
<code>* on selected DB2 system catalog tables.  This is necessary for        </code> 
<code>* certain product features to function properly.                       </code> 
<code>STSYSCAP=N                                                             </code> 
<code>/*                                                                     </code> 
</pre>
</div>  


<h3 id="4.6">4.6 Grant Package Execution</h3> 

<p>Customize the job CDCD.SCHCCNTL(CDCGRNTA) to grant Db2 authorities to the userid for the started task.</p> 
<p>The objects that the started task ID needs execute authority on are PACKAGE CHCPK165.* and PACKAGE CHCPK265.*</p>

<h3 id="4.7">4.7 Bind CDC Plan</h3> 
<p>Customize the job CDCD.SCHCCNTL(CDCBNDPL) to bind the Db2 Packages and Plans that will be used by CDC Replication to access the DB2 log, metadata tables, and application data tables that are to be replicated.</p> 
<p>30 packages and 15 plans will be bound.</p>

<h3 id="4.8">4.8 Create Log Cache ( Single Scrape )</h3> 
<p>Customize the job CDCD.SCHCCNTL(CDCCRCCH) to allocate the VSAM cluster that will back up the Log Cache (which was configured <b>CDCD.CHCDBM65</b>).</p> 

<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre> 
<code>  DEFINE -                                                              </code> 
<code>    CLUSTER -                                                           </code> 
<code>     (NAME(CDCD.CHCCACHE)                                             - </code> 
<code>      VOL(*) -                                                          </code> 
<code>      RECORDSIZE(32760 32760) -                                         </code> 
<code>      NUMBERED -                                                        </code> 
<code>      SHAREOPTIONS(2))-                                                 </code> 
<code>    DATA -                                                              </code> 
<code>     (NAME(CDCD.CHCCACHE.DA)                                          - </code> 
<code>      BUFFERSPACE(8388608) -                                            </code> 
<code>      CYLINDERS(600))                                                   </code> 
</pre>
</div> 

<br><hr>

<h2 id="5.0">5. Configure the z/OS Environment</h2>
<p>Every z/OS environment will have different constraints and considerations for deployment. 
This worked example was implement on a Z Development and Test v13 environment, using the z/OS v2.4 stack provided by ADCD v13. 
The z/OS customizations that I needed to do were as follows</p>

<h3 id="5.1">5.1 APF Authorised Load Libraries</h3>
<p>The CDC Load Libraries need to be APF Authorized. 
With the ADCD distribution I simply added these libraries to ADCD.Z24C.PARMLIB(PROGAD)</p>

<div class="w3-container" style="color:#00FF00; background-color:#000000">   
<pre>
<code>/**********************************************/                      </code>
<code>/*  IIDR 11.4                                 */                      </code>
<code>/**********************************************/                      </code>
<code>APF ADD                                                               </code>
<code>    DSNAME(CDCD.SCHCLOAD)                               VOLUME(USER0B)</code>
</pre>
</div>	
	
<h3 id="5.2">5.2 TCPIP Ports</h3> 
<p>CDC for Db2 z/OS is configured to listen on port 6789, as per the editing CDCD.CHCCMM65. 
The ADCD z/OS v2.4 distribution does not lock high ports, so I didn’t have any network administration to perform to open port 6789.</p> 

<h3 id="5.3">5.3 RACF Started Task ID</h3> 
<p>For the ZD&T environment I didn’t bother the define a started task ID to RACF. 
I just added the PROC to ADCD.Z24C.PROCLIB and ran it as START1.</p>

<h3 id="5.4">5.4 Test Start the CDC Server</h3> 
<p>This is a good point to start the server and resolve any problems with the CDC Server.  
You can test CDC as a Job using the JCL in <code style="color:#00FF00; background-color:#000000">CDCD.SCHCCNTL(CHCPROC)</code>, and then deploy it as a started task later on.
Upon first start, you should expect to see the Classic CDC Server come up.
</p>

<center><img src="/recipes/images/neale/cdc/cdcd_startup.PNG" alt="CDC Startup Messages" style="border:1px solid black; width:800px"></center> 


<br><hr>

<h2 id="6.0">6. Configure the Db2 z/OS Environment</h2>
<p>There is very little to do here.</p>


<h3 id="6.1">6.1 Alter Source Tables for full row logging</h3>
<p>By default Db2 z/OS only logs enough data to perform rollforward and rollback recovery processing. Unchanged column values are not usually logged, but a data replication 
product needs to see all the before and after column values of the entire row. This is achieved by ALTERing the Db2 table to instruct Db2 to perform full row logging 
for all logged SQL operations.</p>
<p>You don't even need to do this in advance, because the CDC administration tools will detect if "Data Capture None" is in force for any source table, and generate an ALTER 
statement to make the change.</p>

<p><code style="color:#00FF00; background-color:#000000">ALTER TABLE TABSCHEMA.TABNAME DATA CAPTURE CHANGES</code></p> 

<h3 id="6.2">6.2 Db2 configuration considerations</h3> 
<p>There is nothing else that needs to be done for a basic up and running exercise.</p>
<p>As you progress with CDC for Db2 z/OS you may need to perform some Db2 tuning. Examples of the sorts of work you may need to do are:</p>

<ul> 
<li>Db2 log buffer configuration should be adjusted to ensure that all log reads for Db2 are satisfied from memory
<li>For Ultra-active Db2 systems you will want to schedule the ALTER TABLE commands for a quiet time
<li>If you replicate a lot of tables, the increase in logging may prompt you to review your log configuration.
</ul>


<p>That's it for the CDC Server !!!</p>

<br><hr>

<h2 id="7.0">7. Integrate with the wider CDC Landscape</h2>
<p>Now that the mainframe CDC Capture Server is ready for business, you will need to start using some non-mainframe tools in order 
to define Subscriptions from them to target systems. Section 7 covers these matters.</p>

<h3 id="7.1">7.1 Deploy CDC as a Started Task</h3> 
<p>Assuming you want to run CDC as a started task, rather than a batch job, you should copy 
the contents of the JCL to run CDC as a batch job <code style="color:#00FF00; background-color:#000000">CDCD.SCHCCNTL(CHCPROC)</code> 
to your PROCLIB, and follow your site standards for establishing a new started task.</p> 


<h3 id="7.2">7.2 Connect from Management Console to CDC Started Task</h3>
<p>This document is primarily concerned with everything that needs to be done to establish CDC for Db2 z/OS as a started task.</p>
<p>Using the the CDC administration tools is now a standard CDC task which is covered in 
the <a href="/recipes/docs/NA_CDC/cdcu2.html">Devops Options for CDC.</a> paper.</p>


<h3 id="7.3">7.3 Use CHCCLP Scripting for z/OS</h3>
<p>CDC Replication is traditionally a Windows-centric environment for operations and control, but it also has advanced scripting capabilities for automation.</p>
<p>The CDC Management Console is a comprehensive GUI that addresses all parts of the devops 
lifecycle ( access control, definition, operations, monitoring ).</p>
<p>The CHCCLP Scripting tools offer automated devops controls using scripts. These can be executed from the Windows-based 
Management Console, or from the Access Server on Windows or Linux. 
The CHCCLP scripting option will be attractive to all shops that wish to implement strong devops governance and control to their 
CDC replication environments. Shops with a z/OS operation bridge should know that the CHCCLP scripting environment can also be deployed 
inside z/OS, either from unix system services (USS) or from JCL (using the java batch scheduler).<p>
<p>All of these devops options are covered in the the <a href="/recipes/docs/NA_CDC/cdcu2.html">Devops Options for CDC.</a> paper 
and <a href="/recipes/docs/NA_CDC/cdcu3.html">CHCCLP Scripting.</a> paper in this series of articles.</p>

<h3 id="7.4">7.4 Conforming to site standards for cross-platform devops and security.</h3> 
<p>So far, this document has been primarily concerned with the mechanics of making CDC operate from VSAM to any number of heterogeneous targets. 
It may be hard to believe, but that was the easy part!</p>
<p>The author has worked with several mainframe customers deploying CDC for VSAM to feed a stream of changes to midrange and Cloud targets. 
The challenges to overcome will include...</p>
<ul>
<li>different development and operational teams supporting the capture and apply services
<li>co-ordinating devops tasks between cross-platform teams
<li>change control procedures
<li>implementing TLS encryption between the application-transparent z/OS platform and application-controlled LUW platforms
</ul>
<p>A good approach is to start by considering the non-functional requriements for the business service that CDC will support. If the business 
requires a high level of service ( low latency, stringent monitoring and alerting, minimal downtime, fast recovery from outages etc... ) then
an operational support model can be developed to meet those requirements.</p>

<p>Once the required service levels are defined, that is a useful reference point for assessing whether the opertional 
management controls and interfaces between different operations teams can satisfy those service levels</p>
<p>In some cases, the co-operation between different operational teams can be adjusted to satisfy the required service levels</p>
<p>In other cases, it may be helpful to use technology options to shift the CDC operations entirely to mainframe, or entirely to non-mainframe. 
This case be done by selecting different CDC agents in many cases, as follows</p>
<ul>
<li>If a Windows/Linux operations hub is desired, then there are remote capture agent options for VSAM and DB2 z/OS.
<li>If a z/OS operations hub is desired, then the Linux-based CDC agents can be deployed as software containers inside z/OS Container Extensions
</ul>
<p>Please be aware of the flexible CDC deployment options that exist, and take an early view on what choices may provide the best 
devops lifecycle proposition for your organisation.</p>

<p>This series of articles includes a heavy focus of worked deployment examples, but the articles in the "Using CDC" column do aim 
to address the practical devops challenges with recommendations on how to address common challenges.</p>


<br><hr>

<h2 id="App">Appendix</h2>
<p>placeholder</p>






<!-- End of Page Content   -->

	<div class="w3-center w3-dark-grey" style="max-width:1100px;margin-top:80px;margin-bottom:80px">
	<p>copyright &copy; zeditor.org</p>
    </div>
	
</div>
	
<script>
// Slideshow
var slideIndex = 1;
showDivs(slideIndex);

function plusDivs(n) {
  showDivs(slideIndex += n);
}

function currentDiv(n) {
  showDivs(slideIndex = n);
}

function showDivs(n) {
  var i;
  var x = document.getElementsByClassName("mySlides");
  var dots = document.getElementsByClassName("demodots");
  if (n > x.length) {slideIndex = 1}    
  if (n < 1) {slideIndex = x.length} ;
  for (i = 0; i < x.length; i++) {
    x[i].style.display = "none";  
  }
  for (i = 0; i < dots.length; i++) {
    dots[i].className = dots[i].className.replace(" w3-white", "");
  }
  x[slideIndex-1].style.display = "block";  
  dots[slideIndex-1].className += " w3-white";
}
</script>
	
</body>
</html>	
	
</body>
</html>
	

