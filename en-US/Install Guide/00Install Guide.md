This chapter provides guidance and examples for the installation and deployment process of YashanDB products.

## Installing the Database Server

> **Note**: 
>
> - During the environment configuration and product installation process, the server may need to be restarted. If there are other applications on the server, please ensure that it will not cause any impact.
> - This chapter is a guide for installing YashanDB in a new environment. If YashanDB is already running in an environment, an upgrade, rather than a fresh installation, is needed. Please refer to [Upgrade](../All Manuals/Installation and Upgrade/Upgrade/00Upgrade) for specific operations.

Please follow the order of operations below:

1. Before executing the installation program, please read the [Pre-Installation Preparation](Pre-Installation Preparation/00Pre-Installation Preparation) document to understand the product's requirements for hardware and software environments. Configure accordingly as per the guidance to ensure the product runs in the best environment.

2. Execute the installation program. YashanDB provides three installation methods:

   - [Command Line Installation](YashanDB Installation via CLI/00YashanDB Installation via CLI): Command line installation requires logging into the server, and all parameters during the process are configured manually.

   - [Graphical User Interface Installation](YashanDB Installation via GUI/00YashanDB Installation via GUI): After starting the web service on the server, users can choose to input the URL in the local browser and follow the prompts on the webpage for a simplified visual installation process.

   - [Docker Installation](YashanDB Installation via Docker): Achieves rapid and lightweight deployment through Docker containers and specific image packages, suitable only for personal development testing, and only supports deploying a Standalone Deployment database.

3. After a successful installation, please continue reading the [Initial Environment after Installation](Initial Environment after Installation/00Initial Environment after Installation) document to understand various initial information about YashanDB, such as users, processes, etc., and configure and adjust as needed, such as creating users and tablespaces.

Both command line and graphical installation of YashanDB are based on the [yasboot tool](../All Manuals/Tools Guide/yasboot/00yasboot), with the main execution flow being:

1. Generate a configuration file containing all information of cluster/group/node.

2. Prepare the deployment environment for all nodes, including automatically creating the HOME directory and running log directory, synchronizing the installation package to all servers, and decompressing it, etc.

   > **Note**: 
   >
   > A node refers to an operating environment that can provide a certain level of service capability (management, computing, storage, etc.). In Standalone Deployment and YAC Deployment, one server constitutes one node.

3. Start all yasom processes and yasagent processes.

   Only one yasom process is required globally (throughout the entire YashanDB environment), but each server requires one yasagent process.

4. Deploy the database instances for all nodes.

## Installing the Database Client

YashanDB provides client installation programs for Windows and Linux environments (the *yasql* command line client tool) for remote connection and operation of YashanDB. Please obtain the corresponding software package and follow the guidance for [YashanDB Client Installation](YashanDB Client Installation/00YashanDB Client Installation) to execute the client installation configuration.