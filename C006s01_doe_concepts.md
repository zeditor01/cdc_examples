# DOE terms and concepts
To get started, it's important to have a clear understanding of some key DOE terms and concepts. 

**Note:** The following definitions mention specific roles within a DOE environment. These roles are defined in  [DOE roles and responsibilities](./C006s02_doe_roles_responsibilities.md).


-	**Subsystems** - The z/OS discovery services provided by UMS are invoked automatically when UMS is started. It discovers and store information from all Db2 subsystems in the z/OS environment where it is running. After this initial discovery *super administrators* can manually run subsystem discovery by refreshing the subsystem information. 
*Super administrators* can view the major characteristics and register the subsystems under UMS/DOE for later use. Registered subsystems can be used by DOE to perform the following tasks:
    - Create environments and associate them with the registered subsystems
    - Define a group or set of objects in a subsystem as *applications*
    - Provision application instances in a subsystem defined in an environment
    
These tasks are illustrated later in this document when performing [Registering subsystems](./C006s02a_doe_landscape_ss.md#registering-db2-subsystems). 

-	**Environments** - An environment is a collection of Db2 subsystems that is used by *teams* to provision *application instances*. Provisioning rules are also associated with each subsystem supported by the environment. *Super administrators* can create, edit, or delete environments. See [Creating environments](./C006s02a_doe_landscape_ss.md#creating-environments).
-	**Teams** - A team is a group of users who work together within a defined application development or test environment. Each team must be associated with the **environments** that it can use.
*Super administrators* can create teams, assign environments to teams, and add and delete team administrators and members. See [Creating teams](./C006s02a_doe_landscape_ss.md#creating-teams).
-	**Applications** - An application is a set of objects such as tablespaces, tables, and indexes, that are grouped together so that they can be managed and provisioned as a single unit for the use of an application program or a set of application programs. Application objects are logical, which means that they are only references to the actual objects. When users provision instances of an application, the referenced objects are copied to create the instances. *Super administrators* and *team administrators* can register, change the settings of, or delete applications and assign them to a team. 

     An application definition is similar to an application component. The objects within an application are assigned to a team, and that team owns those application objects, which means that the team is responsible for them. They are authorized to create  an instance, to change the design of those application objects (at least in their sandbox), and commit code changes, just like a C, COBOL, or Python programmer who is part of a team that's associated with an application component. See [Registering applications](./C006s03_doe_dba.md#registering-applications).

-	**Objects** - Objects are the Db2 resources that are required to run applications such as a Db2 database, table spaces, tables, indexes, stored procedures, user-defined functions, and so forth. 
-   **Instances** - An *application instance* (referred in this paper as an *instance*) is a set of application objects that has been provisioned into the associated environment's subsystem. An instance can be provisioned only by the members of the team that was assigned to an application and its environment. This document focuses on handling instances throughout a CI/CD pipeline; however, tasks related to provisioning, editing, and deprovisioning an instance can be performed by using  the DOE interface called the IBM Unified Experience for z/OS. See [Provisioning an application instance](./C006s03_doe_dba.md#provisioning-an-application-instance) in this document and [IBM Unified Management Server for z/OS - Managing instances
](https://www.ibm.com/docs/en/umsfz/1.1.0?topic=experience-managing-instances) for more details.

