# Setting up a code repository in your development environment

The next step is to set up a connection to the source control management system (SCM) and to set up the SCM workspace. For our demo, we're using GitHub. If you use a different SCM such as GitLab, Bitbucket, Rational Team Concert, or Subversion, the set up process might be slightly different. Refer to the documentation for the SCM tool that you're using.

### Installing Git
Before you start using Git, you have to make it available on your computer, and it’s a good idea to always install or upgrade to the latest version. 

You can download the [latest version](https://git-scm.com/downloads) and follow the section of [the instructions for installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) for the operating system that you're using. After the installation is complete, you will have the Git command line interface, the Git GUI, the Git Bash shell, and a variety of other tools. All of these features are entirely free, and we use many of them along with VS Code in this document.

Alternatively, you can install one of the [Git clients](https://git-scm.com/downloads/guis) in which Git is included. Some of the Git clients listed in the link are free.

### Connecting to the code repository with SSH
By using the SSH protocol, you can connect and authenticate to remote servers and services. With SSH keys, you can connect to GitHub without supplying your username and personal access token at each visit.
See [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) to set up the SSH key.

### Cloning the code repository
There are two ways to clone the GitHub code repository to your local machine: by using Git commands or by using VS Code.

- To clone the code repository to your workspace by issuing commands from the command line or terminal: 
1. Choose or create the directory you want to persist the local code repository as the parent folder, and change to that directory from the command line or terminal.
   
2. Type in the following command:
   `````
   # git clone git@github.com:YourOrganization/YourRepository.git
   `````
  where:
  - YourOrganization is the organization or user name under which your code repository is created.
  - YourRepository is the name of the code repository.
   
  3. Press "Enter" to create your local clone.
   `````
   # git clone git@github.com:YourOrganization/YourRepository.git
   > Cloning into `Cool-Project`...
   > remote: Counting objects: 10, done.
   > remote: Compressing objects: 100% (8/8), done.
   > remove: Total 10 (delta 1), reused 10 (delta 1)
   > Unpacking objects: 100% (10/10), done.
   `````
Refer to [Clone a repository with Git commands](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository#cloning-a-repository-using-the-command-line) for more details.

- To clone a repository to your local workspace directly through VS Code, complete the instructions in [Clone a repository in VS Code](https://code.visualstudio.com/docs/editor/github#_cloning-a-repository).

You're now ready to start making code changes to the application in VS Code.
