The SA_POLICY_ADMIN package provides a set of built-in procedures for setting and removing the association between tables and LBAC label policies.

- This high-level package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this advanced package must have the LBAC_DBA role.

## APPLY_TABLE_POLICY

```plsql
SA_POLICY_ADMIN.APPLY_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    table_options IN VARCHAR DEFAULT NULL,
    label_function IN VARCHAR DEFAULT NULL,
    predicate IN VARCHAR DEFAULT NULL);
```
This stored procedure is used to apply the target LBAC label policy to the target table, and it will automatically add a column to the target table to record the label for each row (the column name will be the `column_name` specified in the target LBAC label policy). 

After applying the policy, the corresponding read/write controls take effect immediately.

> **Note**:
>
> It is recommended to complete all row-level access control-related configurations **before inserting any data** into the table — ideally, during the table creation phase. If the target table already contains data before applying the policy, the system will not automatically assign label values to historical data — such unlabeled rows will only allow read/write access to the `sys` user. To enable other users to access these rows, you must manually assign label values to them using an `UPDATE` statement executed by the `sys` user.

|Parameter |Description |
| :--- | :---- |
| policy_name        | The name of the target LBAC label policy to be applied, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| schema_name    | The username                                               |
| table_name     | The table name                                                  |
| table_options   | Mandatory control option name, default value is NULL <br />Refer to [SA_SYSDBA](SA_SYSDBA.md#defaultoptions) for relevant explanations of mandatory control options <br />If the parameter value is NULL, it takes the value of default_options specified in [SA_SYSDBA](SA_SYSDBA) when CREATE_POLICY was called; if the default_options is also NULL, then table_options will be "READ_CONTROL, WRITE_CONTROL" |
| label_function | Reserved parameter                                                                               |
| predicate      | Reserved parameter                                                                               |

***Example*** for Heap tables

```plsql
BEGIN
    SA_POLICY_ADMIN.APPLY_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', 'READ_CONTROL');
END;
/

PL/SQL Succeed.
```

## REMOVE_TABLE_POLICY

```plsql
SA_POLICY_ADMIN.REMOVE_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    drop_column IN BOOLEAN DEFAULT FALSE);
```
This stored procedure is used to remove the association between a table and the specified LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name    | The name of the LBAC label policy                                                             |
| schema_name    | The username                                                                                     |
| table_name     | The table name                                                                                  |
| drop_column    | Whether to drop the label column, default value is FALSE                                        |

***Example*** for Heap tables

```plsql
BEGIN
    SA_POLICY_ADMIN.REMOVE_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', true);
END;
/

PL/SQL Succeed.
```
