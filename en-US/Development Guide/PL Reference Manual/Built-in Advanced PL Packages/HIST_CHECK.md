The HIST_CHECK advanced package provides a set of built-in stored procedures/functions to enable/disable tamper-proof functionality for specified tables and to check whether tampering has occurred on specified tables.

Tampering mainly refers to performing operations such as insertions, deletions, or updates on the target table. The execution methods include:

- Direct modification: Executing SQL statements such as INSERT/DELETE/UPDATE/TRUNCATE on the table.
- Indirect modification: Importing table data via LOAD DATA, modifying table data using the DBMS_LOB advanced package, etc.

This built-in advanced package is only applicable to HEAP tables in Standalone Deployment and cannot be used for system tables.

Enabling/disabling/checking requires the current user to have READ/SELECT privileges on the specified table.

## ENABLE_CHECK

```plsql
HIST_CHECK.ENABLE_CHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

This stored procedure is used to enable tamper-proof ability for the specified table.

|Parameter |Description |
|:-----------| :---- |
| user_name  | Username     |
| table_name | Table name   |

***Example*** for Standalone Deployment Heap tables

```plsql
BEGIN
    HIST_CHECK.ENABLE_CHECK('sales','employees');
END;
/
```

## DISABLE_CHECK

```plsql
HIST_CHECK.DISABLE_CHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

This stored procedure is used to disable tamper-proof ability for the specified table.

|Parameter |Description |
|:-----------| :---- |
| user_name  | Username     |
| table_name | Table name   |

***Example*** for Standalone Deployment Heap tables

```plsql
BEGIN
    HIST_CHECK.DISABLE_CHECK('sales','employees');
END;
/
```

## ACHECK

```plsql
HIST_CHECK.ACHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

This function is used to check whether tampering has occurred on the specified table. The return result is TRUE or FALSE.

- TRUE: Indicates that no tampering has occurred or the tamper-proof functionality has not yet been enabled.
- FALSE: Indicates that tampering has occurred since the tamper-proof functionality was enabled.

|Parameter |Description |
|:-----------| :---- |
| user_name  | Username     |
| table_name | Table name   |

***Example*** for Standalone Deployment Heap tables

```plsql
-- When the specified table has not enabled tamper-proof ability or has not been tampered with, the check function returns TRUE.
SQL> SELECT HIST_CHECK.ACHECK('sales', 'employees') employees FROM dual;

EMPLOYEES
--------------------
true

-- When the specified table has enabled tamper-proof ability and a tampering operation occurs, the check function returns FALSE.
SQL> EXEC HIST_CHECK.ENABLE_CHECK('sales','employees');

SQL> INSERT INTO sales.employees VALUES ('0101','000','0101000001','Mask','1',SYSDATE-1000);

SQL> SELECT HIST_CHECK.ACHECK('sales', 'employees') employees FROM dual;

EMPLOYEES
--------------------
false

-- Disable the tamper-proof functionality for the specified table.
SQL> EXEC HIST_CHECK.DISABLE_CHECK ('sales','employees');

```
