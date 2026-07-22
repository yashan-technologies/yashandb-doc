The SA_SYSDBA package provides a set of built-in procedures for creating and deleting LBAC label policies.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- The user executing this advanced package must have the LBAC_DBA role.

## CREATE_POLICY

```plsql
SA_SYSDBA.CREATE_POLICY (
    policy_name IN VARCHAR,
    column_name IN VARCHAR DEFAULT NULL,
    default_options IN VARCHAR DEFAULT NULL);
```
This stored procedure is used to create an LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name | The name of the LBAC label policy, must be unique within the database and not exceed 64 bytes in length |
| column_name | The name of the label column, must comply with column [naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers) , and different policies are not allowed to use the same column name. When set to the default value `NULL`, it defaults to `{policy_name}_COL`<br />When this policy is applied to the target table, the system will automatically add a column (named using this parameter value) to the target table to record the label for each row |
| default_options | Default mandatory control option(s), multiple option names separated by commas (`,`), defaulting to `NULL`<br />If mandatory control options are not explicitly specified when applying this policy, the value of this parameter will be used by default |

<span id="defaultoptions" name="defaultoptions"></span>
Explanations of mandatory control options:

|Name |Value |Description |
| :--- | :---- | :---- |
| READ_CONTROL | 1 | Control read |
| INSERT_CONTROL | 2 | Control insert |
| UPDATE_CONTROL | 4 | Control update |
| DELETE_CONTROL | 8 | Control delete |
| WRITE_CONTROL | 14 | Control write, including INSERT_CONTROL, UPDATE_CONTROL, and DELETE_CONTROL |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_SYSDBA.CREATE_POLICY ('lbac_policy', 'policy_column', 'READ_CONTROL');
END;
/

PL/SQL Succeed.
```

## DROP_POLICY

```plsql
SA_SYSDBA.DROP_POLICY (
    policy_name IN VARCHAR,
    drop_column BOOLEAN DEFAULT FALSE);
```
This stored procedure is used to delete an LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name | The name of the LBAC label policy to be deleted, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| drop_column | Whether to simultaneously delete the label column in the table associated with this policy, defaulting to `FALSE` (i.e., do not delete) |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_SYSDBA.DROP_POLICY ('lbac_policy', true);
END;
/

PL/SQL Succeed.
```
