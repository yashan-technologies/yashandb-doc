The SA_COMPONENTS package provides a set of built-in subprograms for creating, modifying, and deleting policy components, which include levels and compartments.

- This high-level package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this high-level package must have the LBAC_DBA role.

## CREATE\_LEVEL

```plsql
SA_COMPONENTS.CREATE_LEVEL (
    policy_name IN VARCHAR,
    level_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
This stored procedure is used to create a level for LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC security policy |
| level_num    | The level value, ranging from [0,9999] |
| short_name   | Short name                           |
| long_name    | Long name                            |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_COMPONENTS.CREATE_LEVEL ('lbac_policy', 10, 'GENERAL', 'GENERAL');
END;
/

```

## DROP\_LEVEL

```plsql
SA_COMPONENTS.DROP_LEVEL (
  policy_name IN VARCHAR,
  level_num IN INTEGER);
```
This stored procedure is used to delete a level from the LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC security policy |
| level_num    | The level value                      |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_COMPONENTS.DROP_LEVEL ('lbac_policy', 10);
END;
/

```

## CREATE\_COMPARTMENT

```plsql
SA_COMPONENTS.CREATE_COMPARTMENT (
    policy_name IN VARCHAR,
    comp_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
This stored procedure is used to create a compartment for LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC security policy |
| comp_num     | The compartment value, ranging from [0,9999] |
| short_name   | Short name                           |
| long_name    | Long name                            |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_COMPONENTS.CREATE_COMPARTMENT ('lbac_policy', 10, 'MNG', 'MNG');
END;
/

```

## DROP\_COMPARTMENT

```plsql
SA_COMPONENTS.DROP_COMPARTMENT (
  policy_name IN VARCHAR,
  comp_num IN INTEGER);
```
This stored procedure is used to delete a compartment from the LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name  | The name of the LBAC security policy |
| comp_num     | The compartment value                |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_COMPONENTS.DROP_COMPARTMENT ('lbac_policy', 10);
END;
/

```
