*yfscmd* is the YFS management tool. Users can use this tool for basic management of YFS, such as adding, deleting, querying, and modifying files and directories.

When executing the *yfscmd* subcommands (including [Disk Management Commands](Disk Management Commands) and [File Management Commands](File Management Commands)), it is essential to ensure that the cluster management service (YCS) is started and running normally; otherwise, the command cannot be executed successfully.

The operating environment for *yfscmd* also depends on the YCS environment variable `$YASCS_HOME`, which must be correctly set.

The *yfscmd* tool provides some experimental features, such as aliases, bash, and some file management commands. Some high-risk commands may alter the data on shared storage, and incorrect operations may corrupt or lose database data files. **It is not recommended to use this in a production environment.**