## Delete Initial Database (yashan Mode)

The initial database may not meet the actual needs of the production environment. You can delete it as needed and create a production database.

This operation is not applicable to ISC Distributed Cluster Deployment and Standalone Deployment in mysql mode.

1. Log in to the database as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version} x86_64 - Linux

    SQL> 
    ```
    
2. Use the [SHUTDOWN](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/SHUTDOWN) statement to shut down the initial database.

3. Restart the instance in NOMOUNT mode. For specific operations, please refer to [Database Instance Startup and Shutdown](../../All Manuals/Database Administration/Basic Database Management/Instance Startup and Shutdown).

4. Execute the [DROP DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/DROP DATABASE) statement to delete the initial database.

5. Execute the [CREATE DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE DATABASE) statement to create a new database.

## Create Business Database (mysql Mode)

1. Log in to YashanDB as the sys user.

2. Create a user and grant appropriate privileges. For specific operations, please refer to [User](../../All Manuals/Product Security/Identity Identification and Authentication/User/Managing Users (mysql Mode)) and [Privilege Granting and Revocation](../../All Manuals/Product Security/Data Access Control/Privilege Management (mysql Mode)/Permission Granting and Revocation).

3. Log in to YashanDB as the new user.

4. Execute the [CREATE DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/CREATE DATABASE) statement to create a new database. In subsequent usage, you can choose the database to operate on in a session using the [USE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/USE) statement.

## Start Using YashanDB

Before developers or other users can use YashanDB, the system administrator must perform the following related operations:

- If you need to learn how to use YashanDB according to this manual, please create a sales example user and grant appropriate privileges.
  
  ```sql
  CREATE USER sales IDENTIFIED BY 'Yr_pswd0';
  GRANT DBA TO sales;

  -- In mysql mode, you also need to execute the following operations
  GRANT ALL ON *.* TO sales;
  conn "sales"/********
  CREATE DATABASE IF NOT EXISTS sales DEFAULT CHARACTER SET `utf8` DEFAULT collate `utf8mb4_general_ci`;
  use sales;
  ```

- Refer to [Database Management](../../All Manuals/Database Administration/Basic Database Management/00Basic Database Management) for various database management contents, such as establishing a tablespace management system.

- Refer to [Product Security](../../All Manuals/Product Security/Overview of YashanDB Security Architecture) to establish a database security system.

- For YAC Deployment, also refer to the management contents of [YAC](../../All Manuals/YashanDB for Cluster/Overview of YAC).

- For Distributed Cluster Deployment, please refer to the user guide in [Distributed Cluster](../../All Manuals/分布式集群/用户使用指导) to start using YashanDB.  
