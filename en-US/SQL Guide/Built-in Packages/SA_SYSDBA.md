The SA_SYSDBA package provides a set of built-in procedures for creating and deleting LBAC security policies.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- The user executing this advanced package must have the LBAC_DBA role.

## CREATE\_POLICY

```plsql
SA_SYSDBA.CREATE_POLICY (
    policy_name IN VARCHAR,
    column_name IN VARCHAR DEFAULT NULL,
    default_options IN VARCHAR DEFAULT NULL);
```
This stored procedure is used to create an LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name | The name of the LBAC security policy |
| column_name | The label column name; if the default value NULL is used, the column_name value will be policy_name + "_COL" |
| default_options | The combination of mandatory control option names, default value NULL |

<span id="defaultoptions" name="defaultoptions" class="yaslink"></span>
Explanations of mandatory control options:

|Name |Value |Description |
| :--- | :---- | :---- |
| READ_CONTROL | 1 | Control read |
| INSERT_CONTROL | 2 | Control insert |
| UPDATE_CONTROL | 4 | Control update |
| DELETE_CONTROL | 8 | Control delete |
| WRITE_CONTROL | 14 | Control write, combination of INSERT_CONTROL, UPDATE_CONTROL, DELETE_CONTROL |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_SYSDBA.CREATE_POLICY ('lbac_policy', 'policy_column', 'READ_CONTROL');
END;
/

```

## DROP\_POLICY

```plsql
SA_SYSDBA.DROP_POLICY (
    policy_name IN VARCHAR,
    drop_column BOOLEAN DEFAULT FALSE);
```
This stored procedure is used to delete an LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name | The name of the LBAC security policy |
| drop_column | Whether to delete the label column of the table, default value FALSE |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_SYSDBA.DROP_POLICY ('lbac_policy', true);
END;
/

```
