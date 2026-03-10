The SA_USER_ADMIN package provides a set of built-in procedures to establish and remove the association between users and LBAC security policies.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this advanced package must possess the LBAC_DBA role.

## SET\_USER\_LABELS

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
This stored procedure is used to associate LBAC security labels with a user, thereby establishing the association between the user and LBAC security policies.

|Parameter |Description |
| :--- | :---- |
| policy_name| The name of the policy to which the LBAC security label belongs. |
| user_name| Username. |
| max_read_label| The user's maximum read label. This sets the maximum read privilege label that a user can be assigned, including the maximum level and range combinations. |
| max_write_label| The user's maximum write label. This sets the maximum write privilege label that a user can be assigned, including the maximum level and range combinations. It must meet the following rules:<br/> (1) The level in max_write_label must equal the level in max_read_label.<br/> (2) The content of the range in max_write_label must be a subset of the range content in max_read_label.<br/> (3) If this parameter is empty, its value is max_read_label.<br/> |
| min_write_label| The user's minimum write label. Specifies the user's minimum write label, which only contains levels. If this parameter is empty, its value is the minimum level within the policy. |
| def_label| The default session label, whose range content is a subset of the range content in max_read_label. If this parameter is empty, its value is max_read_label. <br/>The content of def_label is shown in the DBA_SA_USER_LABELS view as default_read label and default_write label.<br/> * default_read: The user's read privilege label, where the level value in the label corresponds to the level of def_label, and the range content in the label is a subset of the range content in max_read_label and def_label.<br/> * default_write: The user's write privilege label, where the level value in the label corresponds to the level of def_label, and the range content in the label is a subset of the range content in max_write_label and def_label.<br/> |
| row_label| Row label, the levels contained cannot exceed the levels in def_label, and the range content is a subset of the range in def_label. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_USER_ADMIN.SET_USER_LABELS ('lbac_policy', 'sales', 'GENERAL:MNG');
END;
/

```

## DROP\_USER\_ACCESS

```plsql
SA_USER_ADMIN.DROP_USER_ACCESS (
    policy_name IN VARCHAR,
    user_name IN VARCHAR);
```
This stored procedure is used to remove the association between a user and the specified LBAC security policy.

|Parameter |Description |
| :--- | :---- |
| policy_name| LBAC security policy. |
| user_name| Username. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_USER_ADMIN.DROP_USER_ACCESS ('lbac_policy', 'sales');
END;
/

```
