## Delete Initial Database (yashan Mode)

The initial database may not meet the actual needs of the production environment. You can delete it as needed and create a production database.

This operation is not applicable to ISC Distributed Cluster Deployment and Standalone Deployment in mysql mode.

1. Log in to the database as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    
2. Use the [SHUTDOWN](../../../Development Guide/SQL Reference Manual/SQL Statements/SHUTDOWN) statement to shut down the initial database.

3. Restart the instance in NOMOUNT mode. For specific operations, please refer to [Database Instance Startup and Shutdown](../../../Database Administration/Instance Management/Instance Startup and Shutdown).

4. Execute the [DROP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/DROP DATABASE) statement to delete the initial database.

5. Execute the [CREATE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE) statement to create a new database.

## Create Business Database (mysql Mode)

1. Log in to YashanDB as the sys user.

2. Create a user and grant appropriate privileges. For specific operations, please refer to [User](../../../Reference Manual of mysql Mode/Product Security/Managing Users) and [Privilege Granting and Revocation](../../../Reference Manual of mysql Mode/Product Security/Privilege Management/Permission Granting and Revocation).

3. Log in to YashanDB as the new user.

4. Execute the [CREATE DATABASE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/CREATE DATABASE) statement to create a new database. In subsequent usage, you can choose the database to operate on in a session using the [USE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/USE) statement.

## Start Using YashanDB

Before developers or other users can use YashanDB, the system administrator must perform the following related operations:

- If you need to learn how to use YashanDB according to this manual, please create a sales example user and grant appropriate privileges.
  
  ```sql
  CREATE USER sales IDENTIFIED BY 'Yr_pswd0';
  GRANT DBA TO sales;

  -- In mysql mode, you also need to execute the following operations
  GRANT ALL ON *.* to sales;
  conn "sales"/********
  create database if not exists sales default character set `utf8` default collate `utf8mb4_general_ci`;
  use sales;
  ```

- Refer to [Database Management](../../../Database Administration/Instance Management/00Instance Management) for various database management contents, such as establishing a tablespace management system.

- Refer to [Product Security](../../../Product Security/Overview of YashanDB Security Architecture) to establish a database security system.

- For YAC Deployment or Distributed Cluster Deployment, also refer to the management contents of [Cluster Management](../../../Database Administration/Cluster Management/00Cluster Management).
