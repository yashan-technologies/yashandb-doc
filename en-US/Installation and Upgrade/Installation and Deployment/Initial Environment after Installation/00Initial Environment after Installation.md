After the installation of YashanDB, the following initial information will be created:

- System Environment Variables

    Environment variables created for the installation user; these variables contain critical information required for the normal operation of YashanDB and should not be deleted.

- Initial Database

    During the installation process, the instance process will be started, and an initial database will be created based on the installation parameters. After installation, its status will be open.

    - Listening Port: If not adjusted during the installation process, the default listening port for the yashan protocol is 1688. If deployed in mysql mode, an additional MySQL protocol listening port will also exist, defaulting to 1690.

    - Configuration Parameters: All values other than those adjusted during the installation process are system default values.

    - User: The system includes a built-in super administrator sys user (the login password is set during installation), and no other regular users.

- YashanDB Supporting Tools

    The supporting tools for YashanDB are installed synchronously during the product installation and are typically located under {YASDB_HOME}/bin. They should not be deleted.

- Related Processes:

  - **yasdb Process**

    yasdb is the main process of YashanDB. After starting the database instance, this process can be monitored in the system.

  - **yasom Process**

    yasom is the operational service process of YashanDB, which accepts *yasboot* commands to issue and control instructions, managing yasagent.

  - **yasagent Process**

    The yasagent process is a stateless operational service process of YashanDB, accepting yasom instructions and executing queries and operations on yasdb or the file system through tools/drivers/commands.

  - **yascs Process**

    The yascs process is the cluster service management process of YashanDB, which will be started after deploying a YAC.