## Db2 for z/OS database code changes in the CI/CD pipeline 

Many companies have a process in place in which database code (schema) change requirements are coordinated with the data modeler, who then designs the logical and physical data model before those changes are implemented by the DBAs in the different environments. 

This chapter describes the requirements and pain points that are associated with the current approach of implementing the database code changes from the perspective of application developers and DBAs. The data modeler will still design the logical and physical data model and provide this input to the DBA. 

It also presents some typical use cases that illustrate how integrating those database code changes with the application changes in the enterprise CI/CD pipeline can address those requirements and pain points. Lastly, it explains the benefits that Db2 DevOps Experience can provide to application developers and DBAs in the context of this processs.


## Requirements and pain points with the current database code change process

DBAs have traditionally been – and still are - the trusted gatekeepers of their companies' data. They're responsible for data availability (backup and recovery), data quality (integrity), and speed of access (performance and scalability) to enterprise data. When a problem occurs with the database access in the production environment, they're the first ones who are called, day or night, to identify the cause of the problem and to solve it. By learning from these types of events over time, companies establish a "book" of rules to minimize the chances of these problems re-occurring. When changes are proposed that violate these rules, DBAs must reject them and encourage other personas to find a better way to achieve their goals. Because of the critical nature of their role, DBAs must be meticulous and diligent when considering database changes, which to other personas is sometimes viewed as being overly cautious and bureaucratic.


Based on our cumulative years of working closely with DBAs, we know that in most organizations database change requests are submitted to DBAs through ticket systems that DBAs must constantly monitor. These requests result in many, mostly simple, database schema change requests every day. Even though most requests are for simple changes, this repetitive process consumes most of a DBA's time, leaving them little or no time for higher-value projects such as performance evaluation, application tuning, and security-related efforts. And as with any process that involves manual changes, there is always the possibility of introducing errors, which, due to ever-shrinking maintenance windows, are becoming increasingly intolerable.
Enforcing standardization is difficult when DBAs develop their individual way to execute the requests. 

Application developers are dependent on DBAs when they make any application code change that requires a corresponding database code change. They typically see the database code change as a completely separate activity to their application development efforts and are frustrated by having to wait for dependent database changes (DDL) to be integrated before their changes can be approved and deployed into the next  environment, potentially jeopardizing the target dates that they committed to.

Additionally, often multiple conversations between the application developer, the DBA, and the data modeler are needed to finalize the change before it can be deployed. This back-and-forth communication is frequently repeated for each environment, magnifying the resulting disruption of continuous flow of change integrations.

A major benefit of DevOps is that it eliminates many of these inefficiencies. However, DevOps comes with a learning curve for the traditional DBA role to work through. It presents a huge culture change to the established way of doing things.


## Typical database-change-related use cases 

From our many conversations with DBAs, we know that it's extremely common for an application change to require an ALTER TABLE ADD COLUMN schema change, which is why we've chosen this use case to illustrate how to implement a CI/CD pipeline in this document.
You can apply the same process to other database artifacts such as views, triggers, and stored procedures.

Other common use cases focus on automated testing of application changes that access data stored in Db2. We briefly explain them below but do not cover them in detail in this document.

The simple use case is the automated testing of an application change that destroys Db2 test data. This use case would simply require the test data to be reloaded as part of the pipeline execution so that the test can be re-executed.

An evolution of this use case is an automated test of an application change with a database code change. In this use case, the database schema change needs to be deployed before masked and pre-generated test data can be loaded.

Last but not least, validating the performance of SQL in a test environment in prepration for deploying it to the production environment is a very popular use case with DBAs. Performance validation is simpler with static SQL because the SQL can be extracted from the Db2 packages and access paths can be analyzed. Dynamic SQL requires the execution of a test case to expose the SQL statement before access paths can be captured and evaluated following the evaluation process for static SQL.


## Introducing Db2 DevOps Experience for integrating database code changes in the enterprise DevOps pipeline

The IBM Db2 DevOps Experience (DOE) delivers Db2 for z/OS DBaaS as an integrated set of features that elevates the Developer and liberates the Administrator. This technology enables you to bridge the gap between the application developer and DBA personas.  

### How DOE affects the different personas:

- **The application developer**: DOE supports self-service, on-demand database operations so that the developer’s sprint can proceed without interruption and without waiting for the DBA to make the database schema changes. Although DBAs retain the ability to approve and control the changes, they can decide if approval can be automated based upon defined rules or if a manual approval is needed. A developer can now work with the database code (DDL) as with the application code; they can create a set of test objects, make modifications that are automatically verified by coded rules (site rules) in DOE, and perform testing. When the database modifications are ready to be integrated, an approval process is initiated for proper review, acceptance, and merging of changes.


- The **DBA**: The Db2 DBA controls the adoption of Db2 schema changes by creation of DOE applications (a collection of Db2 objects that should be provisioned together for this application) and defining site rules that the schema changes must adhere to. The database superuser can define the environments (e.g., development, integration, test, etc.) in which provisioning operations take place and can define which teams can use these environments.

   In large environments, where a DBA might be responsible for dozens or even hundreds of subsystems, the use of DOE APIs can radically increase efficiency and reduce the potential for errors. DBAs can automate the deployment of fully reviewed and approved schema changes to all of the environments that they're responsible for, whereas before, this effort was largely manual, repetitive, and time-consuming and created the potential for introducing a whole host of problems, such as columns being added in different sequences, the introduction of typos, and deploying a change to the wrong environment. By using common environment definitions, the chance of introducing these types of error is significantly reduced.

- The **DevOps Engineer**: The DevOps engineer works jointly with the database superuser to integrate the defined DOE artifacts into the pipeline. Schema (DDL) changes must be maintained in an SCM (e.g., Git) that can trigger the pipeline execution in case of push changes. DOE provides over 100 REST APIs to make all administration and operational services available for integration into the pipeline orchestration and deployment tools.

Implementing an enterprise CI/CD pipeline successfully requires DBAs to fully participate in the DevOps project and to be open to reviewing existing processes for changing database code. 
The DBA must partner with the enterprise DevOps team to learn how to fully leverage the pipeline automation tools to streamline database code deployments through automation.
A successful DevOps implementation shifts the DBA role from deployment to development, freeing them up to provide more value-added services to the organization. It is the foundation for enabling self-service deployments as part of the standard process. 
