[Back to README.md and Table of Contents.](README.md)

# Title Setting Up CDC remote capture agent for Db2 z/OS

Ooops. This article hasn't been written yet. Please check back later.

![Roadwork](images/work_in_progress.jpg)

## Table of Contents


<ul class="toc_list">
<li><a href="#abstract">Abstract</a>   
<li><a href="#1.0">1 Introduction to CDC remote Capture for Db2 z/OS</a>
<ul>
  <li><a href="#1.1">1.1 Requirements to Replicate to Apache Kafka</a></li>
  <li><a href="#1.2">1.2 Kafka Concepts</a></li>
</ul>
<li><a href="#2.0">2. High Level Review of Implementation Steps</a>
<li><a href="#3.0">3. Installing CDC Kafka</a>
<ul>
  <li><a href="#3.1">3.1 Linux paths and permissions</a></li>
  <li><a href="#3.2">3.2 Install the CDC Apply Agent for Kafka</a></li>
  <li><a href="#3.3">3.3 Create the CDC for Kafka Instance</a></li>
</ul> 
<li><a href="#4.0">4. Configure the Linux Environment</a>
<ul>
  <li><a href="#4.1">4.1 TCPIP Ports</a></li>
  <li><a href="#4.2">4.2 Kafka Connection Properties</a></li>
  <li><a href="#4.3">4.3 Likely additional Kafka configuration</a></li>  
</ul>
<li><a href="#5.0">5. Understanding CDC for Kafka as a Kafka Client</a>
<ul>
  <li><a href="#5.1">5.1 TCPIP connections between CDC and Kafka</a></li>
  <li><a href="#5.2">5.2 Kafka Schema Registry considerations</a></li>
  <li><a href="#5.3">5.3 Connecting to Kerberized Kafka</a></li>  
  <li><a href="#5.4">5.4 Console Consumers for testing Subscriptions</a></li>
  <li><a href="#5.5">5.5 Using Kafka Custom Operators (KCOPs)</a></li>
</ul>
<li><a href="#6.0">6. Integrate with the wider CDC Landscape</a>
<ul>
  <li><a href="#6.1">6.1 Automation of the CDC Apply for Kafka Service</a></li>
  <li><a href="#6.2">6.2 Connect from Management Console to CDC for Kafka instance</a></li>
  <li><a href="#6.3">6.3 Use CHCCLP Scripting</a></li>  
  <li><a href="#6.4">6.4 Conforming to site standards for cross-platform devops and security</a></li>
</ul> 
</ul>


<br><hr>


<h2 id="abstract"> Abstract</h2>

This document