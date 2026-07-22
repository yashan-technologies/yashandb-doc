## (Optional) Delete Initial Database (Non-CDB in yashan Mode)

The initial database may not meet the actual needs of the production environment. You can delete it as needed and create a production database.

This operation is not applicable to ISC Distributed Cluster Deployment and Standalone Deployment in mysql mode.

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
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

6. (Optional) Create a user and grant appropriate privileges. For specific operations, please refer to [Managing Users](../../../Product Security/Identity Identification and Authentication/Managing Users) and [Privilege and Role Management](../../../Product Security/Data Access Control/Privilege and Role Management/00Privilege and Role Management).

## Create PDB (CDB)

If a CDB is deployed (the --enable-pluggable-database command option is specified), then PDBs also need to be created.

1. Log in to YashanDB as the sys user.

2. (Optional) Create a user and grant appropriate privileges. For specific operations, please refer to [User and Role System in CDB](../../../Product Security/User and Role System in CDB), [Managing Users](../../../Product Security/Identity Identification and Authentication/Managing Users) and [Privilege and Role Management](../../../Product Security/Data Access Control/Privilege and Role Management/00Privilege and Role Management).

3. (Optional) Log in to the CDB root as the new user.

4. Execute the [CREATE PLUGGABLE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PLUGGABLE DATABASE) statement to create a new PDB.

## Create Business Database (mysql Mode)

1. Log in to the database as sys user. If it is a CDB, the target PDB should be logged in.

2. (Optional) Create a user and grant appropriate privileges. For specific operations, please refer to [Managing Users](../../../Reference Manual of mysql Mode/Product Security/Managing Users) and [Privilege Granting and Revocation](../../../Reference Manual of mysql Mode/Product Security/Privilege Management/Permission Granting and Revocation).

3. (Optional) Re-login with the new user.  

4. Execute the [CREATE DATABASE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/CREATE DATABASE) statement to create a new database. In subsequent usage, you can choose the database to operate on in a session using the [USE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/USE) statement.

## Start Using YashanDB

Before developers or other users can use YashanDB, the system administrator must perform the following related operations:

- If you need to learn how to use YashanDB according to this manual, please create a sales example user and grant appropriate privileges.

    :::tabs
    == Default Example Scenario

```sql 
CREATE USER sales IDENTIFIED BY 'Yr_pswd0';
GRANT DBA TO sales;
```
    == CDB Mode

```shell
-- Create global DBA sample user "c##sales" on the CDB root
SQL> CREATE USER c##sales IDENTIFIED BY 'Yr_pswd0';
SQL> GRANT DBA TO c##sales CONTAINER = ALL;
SQL> exit

# Log in to the target PDB and create sample user "sales"
$ yasql c##sales/********@192.168.1.2:1688/pdb1
SQL> CREATE USER sales IDENTIFIED BY 'Yr_pswd0';
SQL> GRANT DBA TO sales;
```

    == mysql Mode

```sql
CREATE USER sales IDENTIFIED BY 'Yr_pswd0';
GRANT DBA TO sales;
GRANT ALL ON *.* to sales;
conn "sales"/********
CREATE DATABASE IF NOT EXISTS sales DEFAULT CHARACTER SET `utf8` DEFAULT collate `utf8mb4_general_ci`;
use sales;
```

    :::

- Please refer to [Database Management](../../../Database Administration/Instance Management/00Instance Management) for various database management contents, such as establishing a tablespace management system.

- Please refer to [Product Security](../../../Product Security/Overview of YashanDB Security Architecture) to establish a database security system.

- For YAC Deployment or Distributed Cluster Deployment, also refer to the management contents of [Cluster Management](../../../Database Administration/Cluster Management/00Cluster Management).

- If deployed as a CDB (specified the --enable-pluggable-database command option), please also refer to [Multitenant Management](../../../Database Administration/​​Multitenant Management/00​​Multitenant Management).
