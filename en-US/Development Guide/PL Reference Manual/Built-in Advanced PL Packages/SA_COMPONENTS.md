The SA_COMPONENTS package provides a set of built-in subprograms for creating and deleting components of LBAC label policies. These components include levels and compartments.

- This high-level package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this high-level package must have the LBAC_DBA role.

## CREATE_COMPARTMENT

```plsql
SA_COMPONENTS.CREATE_COMPARTMENT (
    policy_name IN VARCHAR,
    comp_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
This stored procedure is used to create a compartment for an existing LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| comp_num | The compartment number, must be unique within the policy and in the range [0, 9999]<br />This value is used to classify the protected data |
| short_name   | The short name of the compartment, must be not exceed 64 characters in length |
| long_name    | The long name of the compartment, must be not exceed 64 characters in length |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_COMPONENTS.CREATE_COMPARTMENT ('lbac_policy', 10, 'MNG', 'MNG');
END;
/

PL/SQL Succeed.
```

## CREATE_LEVEL

```plsql
SA_COMPONENTS.CREATE_LEVEL (
    policy_name IN VARCHAR,
    level_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
This stored procedure is used to create levels for an existing LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| level_num    | The number of the level, must be unique within the policy and in the range [0, 9999]<br />This value is used to determine the sensitivity level of the protected data — the higher the number, the higher the sensitivity |
| short_name   | The short name of the level, must be not exceed 64 characters in length |
| long_name    | The long name of the level, must be not exceed 64 characters in length |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_COMPONENTS.CREATE_LEVEL ('lbac_policy', 10, 'GENERAL', 'GENERAL');
END;
/

PL/SQL Succeed.
```

## DROP_COMPARTMENT

```plsql
SA_COMPONENTS.DROP_COMPARTMENT (
  policy_name IN VARCHAR,
  comp_num IN INTEGER);
```
This stored procedure is used to delete a compartment from an existing LBAC label policy.

If the compartment has already been applied to a label, it cannot be deleted directly.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| comp_num     | The compartment number to be deleted within the target policy, which can be obtained via the `COMP_NUM` column in the [DBA_SA_COMPARTMENTS](../../../Reference Manual/System Views/DBA Views/DBA_SA_COMPARTMENTS) view |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_COMPONENTS.DROP_COMPARTMENT ('lbac_policy', 10);
END;
/

PL/SQL Succeed.
```


## DROP_LEVEL

```plsql
SA_COMPONENTS.DROP_LEVEL (
  policy_name IN VARCHAR,
  level_num IN INTEGER);
```
This stored procedure is used to delete a level from an existing LBAC label policy.

If the level has already been applied to a label, it cannot be deleted directly.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| level_num    | The numeric value of the level to be deleted within the target policy, which can be obtained via the `LEVEL_NUM` column in the [DBA_SA_LEVELS](../../../Reference Manual/System Views/DBA Views/DBA_SA_LEVELS) view |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_COMPONENTS.DROP_LEVEL ('lbac_policy', 10);
END;
/

PL/SQL Succeed.
```