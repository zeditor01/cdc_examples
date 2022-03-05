# Description of the sample application

## Introduction


The examples in this document are based on a sample COBOL application running in a CICS Transaction Server (CICS TS). This application is called the General Insurance application, or GenApp for short. We chose this application because it's provided with CICS TS, which means that it's available to all CICS users. We encourage you to use this application in conjunction with the information in this document to help you explore the capabilities of IBM Db2 DevOps Experience for z/OS.

The primary use case in this document is straightforward: we need to update the GenApp application to include a country code for our customers' telephone numbers.

To do so, we must change the COBOL code that makes up the GenApp application to accept an additional field in the data structure, and we must add a column to the Db2 schema. We'll demonstrate how a developer can change the related COBOL and DDL files and how the changes are picked up by our CI/CD pipeline. We'll focus on the Db2 DDL changes and the process that these changes go through, up to and including deployment.

### About the GenApp application

The GenApp application simulates transactions made by an insurance company to create and manage its customers and their insurance policies. The application provides sample data and a 3270 interface for creating and querying customer and policy information. However, the information in this document doesn't use the 3270 interface but instead demonstrates how to call the business logic by means of RESTful APIs. 

Because the application is designed to simulate the flow of an application, some aspects of the application architecture intentionally don't adhere to coding best practices. However, the application is designed to be extended to demonstrate other ways of accessing and transforming traditional applications that are based on best practices. 

The GenApp application runs in a single CICS region. It writes to a VSAM file and to Db2 for z/OS. As you use the application to explore different features of CICS, the configuration of the application changes to include additional components.
This document focuses on demonstrating how the database schema change can move through the same pipeline as the related application change by exploiting Db2 DevOps Experience capabilities.

For more information about the GenApp application and to obtain a copy of the application, see the
[General insurance application (GENAPP) for IBM CICS TS](https://www.ibm.com/support/pages/cb12-general-insurance-application-genapp-ibm-cics-ts) topic.

### Db2 resources for the GenApp application

The database for the GenApp application contains details about customers and their insurance policies. The set of Db2 objects for this application belongs to one logical Db2 datatabse, and all objects are under the same schema. These objects include:

* A customer table that lists all the customer records, including the customer number
* A policy table that lists all the policies, including the customer number, policy number, and policy type
* A policy risk table for each type of policy: commercial, endowment, house, and motor insurance policies

This document focuses on the customer table and the COBOL logic that's needed to create an additional field in this table and progresses through the following steps:

- First, we introduce the data structure of the COBOL program and the link into the Db2 for z/OS schema.
- Next, we add a field in the COBOL data structure that needs to be reflected in the Db2 schema.
- Then, we add a column that doesn't conform to site rules to demonstrate how the pipeline handles these types of errors.
- Lastly, we correct the error and demonstrate how the changes are processed and deployed.

[Chapter 11](C011_demo_overall.md) provides a more detailed overview of the COBOL code and the Db2 tables involved.




