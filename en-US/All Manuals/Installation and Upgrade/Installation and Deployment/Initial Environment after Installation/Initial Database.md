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

3. Restart the instance in NOMOUNT mode. For specific operations, please refer to [Database Instance Startup and Shutdown](../../../数据库管理/实例管理/实例启停).

4. Execute the [DROP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/DROP DATABASE) statement to delete the initial database.

5. Execute the [CREATE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE) statement to create a new database.

## Create Business Database (mysql Mode)

1. Log in to YashanDB as the sys user.

2. Create a user and grant appropriate privileges. For specific operations, please refer to [User](../../../mysql模式参考手册/产品安全/管理用户) and [Privilege Granting and Revocation](../../../mysql模式参考手册/产品安全/特权管理/权限授予与收回).

3. Log in to YashanDB as the new user.

4. Execute the [CREATE DATABASE](../../../mysql模式参考手册/SQL参考手册/SQL语句/CREATE DATABASE) statement to create a new database. In subsequent usage, you can choose the database to operate on in a session using the [USE](../../../mysql模式参考手册/SQL参考手册/SQL语句/USE) statement.

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

- Refer to [Database Management](../../../数据库管理/实例管理/00实例管理) for various database management contents, such as establishing a tablespace management system.

- Refer to [Product Security](../../../Product Security/Overview of YashanDB Security Architecture) to establish a database security system.

- For YAC Deployment or Distributed Cluster Deployment, also refer to the management contents of [Cluster Management](../../../数据库管理/集群管理/00集群管理).
