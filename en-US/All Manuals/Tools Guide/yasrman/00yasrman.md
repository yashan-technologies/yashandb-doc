*yasrman* is an online backup and recovery tool that is associated with YashanDB. Users can utilize this tool to perform backup and recovery operations for YashanDB, as well as manage backup sets.

The main functionalities implemented by *yasrman* include:

- Support for backing up and recovering databases and archive files on the server side. *yasrman* can replace SQL-based backup and recovery commands, connect to the database, and generate backup sets on the database side or recover backup sets on the database side.

- Support for backing up and recovering databases and archive files on the tool side. *yasrman* can remotely connect to the database, generate backup sets on the tool side, or recover backup sets from the tool side to the remote database.

- Support for backing up and recovering distributed cluster databases on the server side. *yasrman* can connect to all nodes in the distributed cluster, simultaneously perform backup and recovery, and ensure the consistency of distributed transactions.

- Support for querying, deleting, and configuring parameters for backup sets.

- Support for XBSA protocol streaming backup and recovery interface, allowing integration with third-party backup software.
