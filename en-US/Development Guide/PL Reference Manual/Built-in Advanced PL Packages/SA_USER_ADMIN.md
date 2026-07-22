The SA_USER_ADMIN package provides a set of built-in procedures to establish and remove the association between users and LBAC label policies.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this advanced package must possess the LBAC_DBA role.

## SET_USER_LABELS

```plsql
SA_USER_ADMIN.SET_USER_LABELS (
    policy_name     IN VARCHAR,
    user_name       IN VARCHAR,
    max_read_label  IN VARCHAR,
    max_write_label IN VARCHAR DEFAULT NULL,
    min_write_label IN VARCHAR DEFAULT NULL,
    def_label       IN VARCHAR DEFAULT NULL,
    row_label       IN VARCHAR DEFAULT NULL);
```
This stored procedure is used to associate LBAC security labels with a user, thereby establishing the association between the user and LBAC label policies.

Upon successful execution, the user's LBAC label policy information can be obtained through the [DBA_SA_USER_LABELS](../../../Reference Manual/System Views/DBA Views/DBA_SA_USER_LABELS) view.

|Parameter |Description |
| :--- | :---- |
| policy_name| The name of the policy to which the LBAC security label belongs. |
| user_name| The name of the target user |
| max_read_label| Sets the maximum read label for the target user — that is, the highest level of read access granted to the user — which must be specified using the label content (`label_value`), and can be obtained via the `LABEL` column in the `DBA_SA_LABELS` view |
| max_write_label| Sets the maximum write label for the target user — that is, the highest level of write access granted to the user — which must be specified using the label content (`label_value`), and can be obtained via the `LABEL` column in the `DBA_SA_LABELS` view<br />When setting this parameter, the following rules must be satisfied:<br />* For level: The level in `max_write_label` must equal the level in `max_read_label`<br />* For compartment: The compartment content in `max_write_label` must be a subset of the compartment content in `max_read_label`<br /><br />If this parameter is `NULL`, it defaults to the value of `max_read_label` |
| min_write_label| Sets the minimum write label for the target user — that is, the lowest level of write access granted to the user — which must be specified using the short name of the level, and can be obtained via the `SHORT_NAME` column in the `DBA_SA_LEVELS` view<br />If this parameter is `NULL`,  it defaults to the minimum level within the policy |
| def_label| The default session label, which must be specified using the short name of a compartment, and must be a subset of the `max_read_label` compartment. It can be obtained via the `SHORT_NAME` column in the `DBA_SA_COMPARTMENTS` view. <br />If this parameter is `NULL`, it defaults to the `max_read_label` compartment |
| row_label| Row label, the levels contained cannot exceed the levels in def_label, and the range content is a subset of the range in def_label. |

In the DBA_SA_USER_LABELS view, the default session label (the value of the def_label parameter) will be presented in the DEFAULT_READ_LABEL field and the DEFAULT_WRITE_LABEL field.

- DEFAULT_READ_LABEL: The user's default read label. Its level value is the level value of def_label, and its range is the intersection of the ranges of max_read_label and def_label.

- DEFAULT_WRITE_LABEL: The user's default write label. The level value in the label is the level value of the def_label label, and the range content in the label is a subset of the range content of max_write_label and def_label.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_USER_ADMIN.SET_USER_LABELS ('lbac_policy', 'sales', 'GENERAL:MNG');
END;
/

PL/SQL Succeed.
```

## DROP_USER_ACCESS

```plsql
SA_USER_ADMIN.DROP_USER_ACCESS (
    policy_name IN VARCHAR,
    user_name IN VARCHAR);
```
This stored procedure is used to remove the association between a user and the specified LBAC label policy.

|Parameter |Description |
| :--- | :---- |
| policy_name| LBAC label policy. |
| user_name| Username. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_USER_ADMIN.DROP_USER_ACCESS ('lbac_policy', 'sales');
END;
/

PL/SQL Succeed.
```
