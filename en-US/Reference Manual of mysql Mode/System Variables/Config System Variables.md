During the operation of YashanDB (mysql mode), it may be necessary to manually adjust the value of a system variable parameter due to application changes, resource expansion, performance tuning, etc.

## Pre-configuration Preparation

- Modifying system variables in mysql mode requires connecting through a MySQL client for the changes, and the connecting user must be a newly created user in mysql mode with DBA role permissions;

- Before modifying system variables, please refer to [System Variables](./List of System Variables In mysql Mode) to understand the system variables and their value ranges: param and value represent the configuration parameter name and parameter value respectively, and the parameter value must conform to the valid range and format.

- Read-only parameters: Changes cannot be made when the database is in the OPEN state; changes must be made when the database is in the OFF, NOMOUNT, or MOUNT phases.

- Scope: The effective range of the system variable, divided into global and session. In mysql mode, the global variables initialize the session-level variables when a connection is established; modifications to the global variables do not affect the values of session-level variables in already established sessions.

    - GLOBAL: Effective for all clients.

    - SESSION: Effective for the current client.

- Modification methods: Options include:

    - File: Indicates that parameters can be configured by directly editing the my.ini file, affecting all clients.

    - SET statement: Indicates that parameters can be set using the SET statement; modifications can specify their scope but will not be written to the my.ini file.

        - set @@global.param = value

        - set @@session.param = value

        - SET @@param = value (default scope is session if not specified)

## Viewing System Variables

You can query system variable-related information in the following ways:

-  Execute the SHOW GLOBAL|SESSION VARIABLES statement in a session created by connecting to YashanDB (mysql mode) using the MySQL client.

- System variable information can be viewed through the following views:

    - PERFORMANCE_SCHEMA.GLOBAL_VARIABLES: Query global system variables.

    - PERFORMANCE_SCHEMA.SESSION_VARIABLES: Query the system variables of the current session.

## Modifying System Variable Values

Modifying the parameter value of a system variable can be done by executing SET statements or editing the my.ini file.

::: tabs

== SET Statements

Within a session established by a user with DBA privileges (excluding the sys user) via the MySQL client connecting to YashanDB in mysql mode, executing SET and SET NAMES statements can modify the corresponding system variables:

- SET statement: Online modification of system parameters, but changes will not be written to the my.ini file (i.e., the configuration will revert after the database restarts).

- SET NAMES statement: Set the character set for the client, connection, results, and the order of characters in the connection.

- SET CHARSET statement: Set the character set for the client and results.

- SET TRANSACTION statement: Set the transaction isolation level.

Please refer to [MySQL Client](https://doc.yashandb.com/ecosystem/23.4/zh/SQL-Tools/MySQL-Client.html) for the detail of SET statement.

***Example***

```sql
mysql> select @@global.validate_password_length;
+-----------------------------------+
| @@global.validate_password_length |
+-----------------------------------+
|                                 6 |
+-----------------------------------+
1 row in set (0.00 sec)

mysql> set @@global.validate_password_length =4;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.validate_password_length;
+-----------------------------------+
| @@global.validate_password_length |
+-----------------------------------+
|                                 4 |
+-----------------------------------+
1 row in set (0.00 sec)

mysql> select * from PERFORMANCE_SCHEMA.GLOBAL_VARIABLES where VARIABLE_NAME = 'validate_password_length';
+--------------------------+----------------+
| VARIABLE_NAME            | VARIABLE_VALUE |
+--------------------------+----------------+
| validate_password_length | 4              |
+--------------------------+----------------+
1 row in set (0.00 sec)

```

== my.ini File



Modifying system parameters by editing the my.ini file requires a database restart to take effect, and its scope is fixed to global. The my.ini file will not be automatically generated after YashanDB installation; you need to manually create it in the $YASDB_DATA/config path.

1. Check whether the my.ini file exists in the $YASDB_DATA/config path.

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # This example uses /data/yashan/yasdb_data/db-1-1

    $ cd /data/yashan/yasdb_data/db-1-1/config 
    $ ll
    ```

2. Create/edit the my.ini file, save, and exit.



    ```shell
    $ vi my.ini
    # Insert the key-value pairs of the system variables to be modified in the file, or modify the parameter values of existing key-value pairs

    VALIDATE_PASSWORD_LENGTH = 8
    ```
    
3. Restart the database for the configuration to take effect by the database installation user via the yasboot command.

    ```shell
    cd $HOME/install/bin
    yasboot cluster restart -c database_name
    +-------------------------------------------------------------------------------------------------------------+
    | type | uuid             | name                | hostid | index    | status  | return_code | progress | cost |
    +-------------------------------------------------------------------------------------------------------------+
    | task | a4893048b322c2a9 | ReStartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 8    |
    +------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

::: 
