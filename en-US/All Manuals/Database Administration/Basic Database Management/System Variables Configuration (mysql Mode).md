During the operation of YashanDB (mysql mode), it may be necessary to manually adjust the value of a system variable parameter due to application changes, resource expansion, performance tuning, etc.

## Viewing System Variables

You can query system variable-related information in the following ways:

-  Execute the SHOW GLOBAL|SESSION VARIABLES statement in a session created by connecting to YashanDB (mysql mode) using the MySQL client.

- System variable information can be viewed through the following views:

    - PERFORMANCE_SCHEMA.GLOBAL_VARIABLES: Query global system variables.

    - PERFORMANCE_SCHEMA.SESSION_VARIABLES: Query the system variables of the current session.

## Modifying System Variable Values

### Parameter Description

- Scope: The effective range of the system variable, divided into global and session. In mysql mode, the global variables initialize the session-level variables when a connection is established; modifications to the global variables do not affect the values of session-level variables in already established sessions.

- System variables and their value domains: param and value represent the configuration parameter name and parameter value, respectively. The parameter value must conform to the value range and format. For details, please refer to [System Variables (MySQL Mode)](../../Reference Manual/System Variables (mysql Mode)).

### Modification Methods

Modifying the parameter value of a system variable can be done by executing SET statements or editing the my.ini file.

#### SET Statements

You can modify the relevant system variables by executing SET statements and SET NAMES statements in a session created by connecting to YashanDB (mysql mode) using the MySQL client:

- SET statement: Online modification of system parameters, but changes will not be written to the my.ini file (i.e., the configuration will revert after the database restarts).

- SET NAMES statement: Set the character set for the client, connection, results, and the order of characters in the connection.

- SET CHARSET statement: Set the character set for the client and results.

- SET TRANSACTION statement: Set the transaction isolation level.

***Example*** for Standalone Deployment Heap tables

```sql
SET @@SESSION.validate_password_length = 6;
```

#### my.ini File

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
    
3. Restart the database for the configuration to take effect.