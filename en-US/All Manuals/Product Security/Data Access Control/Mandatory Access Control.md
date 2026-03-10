YashanDB supports LBAC, which enables strong access control at the row level. Access to data is controlled based on the security labels of users and the security labels of the data, allowing precise control over users' read and write privileges for each row in a table, ensuring the security of the data being read and written.

## Basic Principle

LBAC is a form of strong access control that applies security policies to protect data in tables. It grants read and write privileges to users based on their security labels. When a user attempts to access protected data, the user's security label is compared with the security label of the protected data, thus constraining or allowing the user's access to the data.

A security policy can be applied to multiple tables. Each time a security policy is applied to a table, an additional column is automatically added for access control related to that policy. After applying a security policy, each row in the table is associated with the corresponding security label to protect the data at the row level.

The security administrator allows users to access the corresponding protected data by granting security labels to users. When a user tries to access protected data, the user's security label is compared with the security label of the protected data:

- Users are only allowed to query rows that are readable under LBAC authorization.
    
    The user's security label must have a level higher than that of the target data, and the range of the user's security label must include all ranges of the target data's security label for the user to read the corresponding row normally.

- Users are only allowed to modify, delete, or insert rows that are writable under LBAC authorization.

    The user's security label must have a minimum level lower than that of the target data, and the range of the user's security label must include all writable ranges of the target data's security label for the user to write to the corresponding row normally.

## Configuring Row Access Control

All configurations related to LBAC are performed by users with the LBAC_DBA role.

LBAC configuration includes creating and managing LBAC security policies, policy components (including levels and ranges), and security labels (Labels) within the policy, as well as applying and removing security policies and managing user security label authorizations.

You can obtain information related to LBAC security policies through the views DBA_SA_COMPARTMENTS, DBA_SA_LABELS, DBA_SA_LEVELS, DBA_SA_POLICIES, DBA_SA_TABLE_POLICIES, and DBA_SA_USER_LABELS.

To use row access control, the following steps must be performed:

1. Query the DBA_YLS_STATUS view to get the status of the row access control functionality. If it is not enabled, call [YLS_ENFORCEMENT](../../开发手册/PL参考手册/内置高级包/YLS_ENFORCEMENT).ENABLE_YLS to enable this functionality.

2. Call [SA_SYSDBA](../../开发手册/PL参考手册/内置高级包/SA_SYSDBA).CREATE_POLICY to create an LBAC security policy.

3. Call [SA_COMPONENTS](../../开发手册/PL参考手册/内置高级包/SA_COMPONENTS).CREATE_LEVEL to create a level for the LBAC security policy, and call SA_COMPONENTS.CREATE_COMPARTMENT to create a range for the LBAC security policy.

4. Call [SA_POLICY_ADMIN](../../开发手册/PL参考手册/内置高级包/SA_POLICY_ADMIN).APPLY_TABLE_POLICY to associate the LBAC security policy with a table.

5. Call [SA_LABEL_ADMIN](../../开发手册/PL参考手册/内置高级包/SA_LABEL_ADMIN).CREATE_LABEL to create a security label for the LBAC security policy.

6. Call [SA_USER_ADMIN](../../开发手册/PL参考手册/内置高级包/SA_USER_ADMIN).SET_USER_LABELS to associate security labels with users, thereby establishing a relationship between users and LBAC security policies.