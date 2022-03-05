# Application development tools for z/OS

The solution described in this document can be applied to any integrated development environment (IDE) that works with a source code management (SCM) system.

One of today's most commonly used IDEs for developing Db2 for z/OS applications is IBM Developer for z/OS (IDz). IDz is a robust toolset for developing and maintaining IBM z/OS applications and offers COBOL, PL/I, High Level Assembler, C/C++, JCL, and Java® development tools on an Eclipse base. With the Enterprise Edition of IDz, developers have the option to choose Microsoft® VS Code™ or Red Hat CodeReady Workspaces for their daily z/OS development work.

Another popular IDE for developing Db2 for z/OS applications is Microsoft Visual Studio Code™ (VS Code). With a large and growing number of extensions available on the VS Code marketplace that support a variety of languages and tasks, such as COBOL, C/C++, Java, JavaScript, Python, Rust, RESTful APIs, Docker, Jenkins, and so on, VS Code's popularity continues to increase. Developers can easily work on different tasks without leaving the VS Code environment.

IBM publishes a pair of VS Code extensions to simplify the development of applications for z/OS and Db2 for z/OS: IBM Db2 for z/OS Developer Extension and IBM Z Open Editor. IBM Db2 for z/OS Developer Extension provides language support for the SQL syntax that is used to define, manipulate, and control data in IBM Db2 for z/OS databases, and IBM Z Open Editor  provides language support for the IBM Enterprise programming languages for z/OS.

### IBM Db2 for z/OS Developer Extension introduction
IBM Db2 for z/OS Developer Extension provides language support for the SQL syntax that is used to define, manipulate, and control data in IBM Db2 for z/OS databases.

The features in this extension simplify the task of developing applications that interact with data in Db2 for z/OS® databases by providing basic productivity features such as:

- SQL formatting
- Code completion and signature help
- Syntax checking and highlighting
- Customizable code snippets

It includes a robust set of capabilities for running SQL, including the ability to:

- Display consolidated results from running multiple SQL statements
- Run SQL that includes parameters and variables from within a native stored procedure (.spsql file)
- Sort query history by the timestamp of the execution
- Select multiple SQL elements on different lines and run those elements as a complete statement
- Restrict the number of rows in SQL result sets
- Quickly identify and display failing SQL statements
- Use null values and retain input values
- Validate XML for host variable parameters input
- Run selected SQL statements from any type of file
- Run SQL with or without parameter markers and host variables and to save SQL execution results
- Commit or rollback the results of SQL executions based on customizable success/failure settings

And it includes the following features for working with stored procedures:

- The ability to deploy, run, and debug native stored procedures
- The ability to set conditional breakpoints when debugging stored procedures

See the [extension link](https://marketplace.visualstudio.com/items?itemName=IBM.db2forzosdeveloperextension) for more details and examples.

### IBM Z Open Editor introduction
IBM Z Open Editor provides language support for the IBM Enterprise programming languages for z/OS®. It supports COBOL 6.3, PL/I 5.3, and High Level Assembler for z/OS 2.4. This support also includes capabilities for embedded statements for CICS 5.6, IMS 15.1.0, and SQL DB2 for z/OS 12.1. Earlier versions of any of these components will also work. 

IBM Z Open Editor enables IBM Z developers to utilize features such as:

- Real-time syntax checking and highlighting while you type
- Problems view with all syntax errors and (in COBOL) unreachable code
- Outline view and outline search
- For both variables and paragraphs:
  - Declaration hovers
  - Peek definition
  - Go to definition
  - Find all references
- Code and variable completion
- Finding and navigating references
- Previewing of included copybooks and include files as well as assembler macros
- Navigating to copybook and including file sources
- Refactoring such as "rename symbol"
- Custom code snippet support and more than 200 high value code snippets for COBOL, PL/I, and JCL out of the box
- Search-and-replace refactoring across multiple program files

See the [extension link](https://marketplace.visualstudio.com/items?itemName=IBM.zopeneditor) for more details and examples.

Both extensions can also run on Eclipse Theia, which is an IDE that runs in a web browser. 

For this demo, we chose VS Code and the two aforementioned IBM extensions to show how these tools can simplify and modernize the experience of developing Db2 applications for the mainframe.

- [Installing VSCode](C009s01_vsc_install.md)
- [Installing Db2 for z/OS Developer Extension and zOpenEditor](C009s02_vsc_extension.md)
- [Setting up development workspace and work on code change](C009s03_vsc_repo.md)
    - [Connect to the code repository with SSH](C009s03_vsc_repo.md#connecting-to-the-code-repository-with-ssh)
    - [Clone the code repository](C009s03_vsc_repo.md#cloning-the-code-repository)
- [Considering data modeling](C009s04_datamodeling.md)
