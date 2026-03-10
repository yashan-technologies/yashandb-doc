YashanDB's MySQL compatibility mode supports controlling SQL syntax and behavior restrictions through the SQL_MODE system variable. Different SQL modes significantly affect database behavior.

## Option Description 

The SQL_MODE system variable is a composite configuration consisting of multiple options. The configurable scope and their corresponding meanings are listed in the table below.

|Option|Description|Is Default|
|----|----|----|
|ALLOW_INVALID_DATES         |Provides syntax compatibility only, with no actual effect on behavior.      |NO      |
|ANSI_QUOTES                 |When the ANSI_QUOTES configuration option is enabled, both double quotes and backticks will be interpreted as identifier quotation symbols, while single quotes are used for string quotation symbols; when ANSI_QUOTES is not enabled, double quotes will be interpreted as string quotation symbols, and backticks will be interpreted as identifier quotation symbols.    |      NO      |
|ERROR_FOR_DIVISION_BY_ZERO  |Provides syntax compatibility only, with no actual effect on behavior.      |YES      |
|HIGH_NOT_PRECEDENCE         |Provides syntax compatibility only, with no actual effect on behavior.      |NO      |
|IGNORE_SPACE                |Provides syntax compatibility only, with no actual effect on behavior.      |YES      |
|NO_AUTO_CREATE_USER         |Provides syntax compatibility only, with no actual effect on behavior.      |YES      |
|NO_AUTO_VALUE_ON_ZERO       |Controls the behavior of AUTO_INCREMENT columns.<br>- When enabled: Inserting 0 into an AUTO_INCREMENT column does not trigger auto-increment; only inserting NULL generates the next sequence number.<br>- When disabled: Inserting either NULL or 0 into an AUTO_INCREMENT column triggers auto-increment.  |YES      |
|NO_BACKSLASH_ESCAPES        |Controls whether the backslash character `\` is treated as an escape character in strings and identifiers. When enabled: Backslashes become ordinary characters (like other letters/numbers), and the default escape sequence in LIKE expressions changes (no longer uses `\`).  |YES      |
|NO_DIR_IN_CREATE            |Provides syntax compatibility only, with no actual effect on behavior.     |NO      |
|NO_ENGINE_SUBSTITUTION      |Provides syntax compatibility only, with no actual effect on behavior. |YES      |
|NO_UNSIGNED_SUBTRACTION     |Provides syntax compatibility only, with no actual effect on behavior.      |YES      |
|NO_ZERO_DATE                |Controls whether '0000-00-00' is allowed as a valid date. When enabled: Insertion of '0000-00-00' is disallowed. |YES      |
|NO_ZERO_IN_DATE             |Controls whether zero values in date fields (e.g., '2023-00-01' or '2023-01-00') are allowed. When enabled: Insertion of such dates is disallowed.   |YES      |
|ONLY_FULL_GROUP_BY          |Provides syntax compatibility only, with no actual effect on behavior.  |YES      |
|PAD_CHAR_TO_FULL_LENGTH     |Controls whether trailing spaces are retained when retrieving CHAR column values and whether CHAR values are padded to their full length. <br>Only applies to CHAR columns; does not affect VARCHAR columns.  |NO       |
|PIPES_AS_CONCAT             | Controls the interpretation of the `||` operator. When enabled, `||` is treated as a string concatenation operator (similar to the CONCAT() function). |NO     |
|REAL_AS_FLOAT               |Controls the interpretation of the REAL data type. When enabled, REAL is treated as a synonym for FLOAT (instead of DOUBLE).   |NO      |
|STRICT_ALL_TABLES           |Provides syntax compatibility only, with no actual effect on behavior.   |YES      |
|STRICT_TRANS_TABLES         |Provides syntax compatibility only, with no actual effect on behavior.     |YES      |
|TIME_TRUNCATE_FRACTIONAL    |Provides syntax compatibility only, with no actual effect on behavior.      |YES     |

## Effective Scope

This system variable supports the following effective scopes:

- Session-level: Takes effect only in the current session after configuration.
- Global-level: Applies to all new sessions after configuration but becomes invalid after restarting the database.
- Global-level after restart: Requires a database restart to take effect across all sessions.

## Example

When configuring system variables in mysql mode, the operation must be performed via the MySQL client or third-party tools. This section demonstrates the configuration process using the MySQL client tool as an example.

::: tabs

== Modify session-level SQL_MODE

```sql
-- Check the sql_mode settings at both global and session levels within the current session
mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

-- Modify the SQL_MODE for the current session and verify it takes effect only locally (global configuration remains unchanged)
mysql> set @@session.sql_mode='STRICT_TRANS_TABLES';
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+---------------------+
| @@session.sql_mode  |
+---------------------+
| STRICT_TRANS_TABLES |
+---------------------+

-- When you need to append SQL_MODE configuration items, you can quickly configure them through an anonymous block. The following example shows how to add the PIPES_AS_CONCAT configuration:

mysql> SET @@SESSION.sql_mode = CONCAT(@@SESSION.sql_mode, ",PIPES_AS_CONCAT");
Query OK, 0 rows affected (0.00 sec)

```

== Modify global-level SQL_MODE

Modifying the global SQL_MODE must be performed by a user with DBA privileges (excluding the sys user). The configuration applies to new sessions only and is not persistent across database instance restarts.

```sql
-- Check the sql_mode settings at both global and session levels within the current session
mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)

-- Modify the grobal-level SQL_MODE
mysql> set @@global.sql_mode='ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.sql_mode;
+-------------------+
| @@global.sql_mode |
+-------------------+
| ANSI_QUOTES       |
+-------------------+
1 row in set (0.00 sec)

-- It doesn't takes effect for the current session
mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

-- create a new connection to the database, and confim the configration of the SQL_MODE being changed
[yashan@host2 ~]$ mysql -h 172.16.60.92 -P 4690 -u username -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 0
Server version: 5.7.42

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select @@session.sql_mode;
+--------------------+
| @@session.sql_mode |
+--------------------+
| ANSI_QUOTES        |
+--------------------+
1 row in set (0.01 sec)

mysql> select @@global.sql_mode;
+-------------------+
| @@global.sql_mode |
+-------------------+
| ANSI_QUOTES       |
+-------------------+
1 row in set (0.01 sec)

-- When you need to append SQL_MODE configuration items, you can quickly configure them through an anonymous block. The following example shows how to add the PIPES_AS_CONCAT configuration:

mysql> SET @@global.sql_mode = CONCAT(@@global.sql_mode, ",PIPES_AS_CONCAT");
Query OK, 0 rows affected (0.00 sec)

```

== Modify global-level SQL_MODE in File



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

    SQL_MODE = ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL
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

4. Establish a new database connection using MySQL Client and check the sql_mode parameter value.

    ```sql
    mysql> select @@global.sql_mode;
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | @@global.sql_mode                                                                                                                                                                                                                                                                |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)

    mysql> select @@session.sql_mode;
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | @@session.sql_mode                                                                                                                                                                                                                                                               |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)

    ```
:::
