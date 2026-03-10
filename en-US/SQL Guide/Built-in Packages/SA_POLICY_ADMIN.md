The SA_POLICY_ADMIN package provides a set of built-in procedures for setting and removing the association between tables and LBAC security policies.

- This advanced package is applicable only to HEAP tables.

- Users executing this advanced package must have the LBAC_DBA role.

## APPLY\_TABLE\_POLICY

```plsql
SA_POLICY_ADMIN.APPLY_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    table_options IN VARCHAR DEFAULT NULL,
    label_function IN VARCHAR DEFAULT NULL,
    predicate IN VARCHAR DEFAULT NULL);
```
This stored procedure is used to associate an LBAC security policy with a table.

|Parameter |Description |
| :--- | :---- |
| policy_name    | The name of the LBAC security policy                                                             |
| schema_name    | The username                                                                                     |
| table_name     | The table name                                                                                  |
| table_options   | Mandatory control option name, default value is NULL <br />Refer to [SA_SYSDBA](SA_SYSDBA.html#defaultoptions) for relevant explanations of mandatory control options <br />If the parameter value is NULL, it takes the value of default_options specified in [SA_SYSDBA](SA_SYSDBA) when CREATE_POLICY was called; if the default_options is also NULL, then table_options will be "READ_CONTROL, WRITE_CONTROL" |
| label_function | Reserved parameter                                                                               |
| predicate      | Reserved parameter                                                                               |

***Example*** for Standalone Deployment Heap tables and YAC Deployment

```plsql
BEGIN
    SA_POLICY_ADMIN.APPLY_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', 'READ_CONTROL');
END;
/

```

## REMOVE\_TABLE\_POLICY

```plsql
SA_POLICY_ADMIN.REMOVE_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    drop_column IN BOOLEAN DEFAULT FALSE);
```
This stored procedure is used to remove the association between a table and the specified LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name    | The name of the LBAC security policy                                                             |
| schema_name    | The username                                                                                     |
| table_name     | The table name                                                                                  |
| drop_column    | Whether to drop the label column, default value is FALSE                                        |

***Example*** for Standalone Deployment Heap tables and YAC Deployment

```plsql
BEGIN
    SA_POLICY_ADMIN.REMOVE_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', true);
END;
/

```
