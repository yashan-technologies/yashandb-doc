After confirming that the authorized user is secure and trustworthy, and that their required privilege matches the actual identity of the user, the user can be granted authorization.

Once it is confirmed that a user or role no longer needs a privilege, it should be promptly revoked. However, before executing the operation, it is essential to carefully confirm that revoking the privilege will not affect normal business operations.

## Privilege Granting

Privileges granted to users and roles become effective immediately. If the target role has been authorized to a user, the user's privileges also become effective immediately.

Privileges granted to a user or role take effect the next time the user logs in. For example, if user A is granted role B, user A does not possess that role before the grant. The role B takes effect when user A logs in again.

When granting privileges, pay attention to the following:

- Avoid granting administrative system privileges to non-administrative users, as possessing administrative system privileges may allow users to perform high-risk operations, such as damaging system tables, leading to severe failures.

- Be cautious when granting object privileges of system users to untrusted users, as this may result in high-risk operations, such as damaging system tables, leading to severe failures.

- When initiating a grant, ensure that the authorized user or role is secure and trustworthy. Also, confirm whether the scope of privileges possessed by the role exceeds this authorization request to prevent granting privileges that exceed business needs, thereby introducing database security risks.

- Be cautious when granting the DBA role to users. Users with the DBA role (when the separation of powers is disabled) are allowed to perform any operation, including damaging system tables, which may lead to severe database failures.

### Prerequisites

Depending on different operational scenarios, the privilege requirements for the grant executor vary:

|Authorization Scenario |Grantor (Executor) |Authorized Object (Recipient) |Optional Privilege Scope |
|--------------------|-------------------|-------------------|-------|
| Direct Grant Based on Privilege| Must meet one of the following conditions:<br/>* Possess the GRANT ANY PRIVILEGE system privilege.<br/>* Possess the SECURITY_ADMIN security admin role. | User<br/>Role (cannot use WITH OPTION)             | System Privilege                |
|                               | Must meet one of the following conditions:<br/>* Be the owner of the target object.<br/>* Possess the GRANT ANY OBJECT PRIVILEGE object privilege.<br/>* Possess the SECURITY_ADMIN security admin role. | User<br/>Role (cannot use WITH OPTION)             | Object Privilege                |
| Indirect Grant Based on Role  | Must meet one of the following conditions:<br/>* Possess the GRANT ANY ROLE system privilege.<br/>* Possess the SECURITY_ADMIN security admin role. | User<br/>Role (cannot use WITH OPTION)<br/>In ISC Distributed Cluster Deployment, roles cannot grant roles | All existing roles              |
| User Privilege Transfer       | Already possesses the target privilege/role and has the corresponding ADMIN/GRANT OPTION. | User<br/>Role (cannot use WITH OPTION)             | Privileges/Roles with<br/>ADMIN/GRANT OPTION possessed by the executor |

### Operational Steps

1. Confirm the privilege requirements of the authorized object:

    - "Least Privilege" Principle: Analyze and organize the minimum privilege set needed by the authorized object based on business requirements. If an existing role carries the appropriate privilege set, consider granting the corresponding role directly to the user.

    - Determine if privilege delegation is needed: If a privilege/role may need to be delegated, specify WITH ADMIN|GRANT OPTION.

2. Log in to YashanDB using a user satisfying the grantor conditions.

3. Execute the [GRANT](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/GRANT) statement to initiate authorization to another user or role.

    ```sql
    -- Directly grant system privilege CREATE ANY TABLE
    GRANT CREATE ANY TABLE TO sales;

    -- Directly grant system privilege SELECT ANY TABLE and allow the sales user to delegate this privilege to other users/roles
    GRANT SELECT ANY TABLE TO sales WITH ADMIN OPTION;
    
    -- Directly grant the SELECT privilege on table area and allow the sales user to delegate this privilege to other users/roles
    GRANT SELECT ON area TO sales WITH GRANT OPTION;
    
    -- Grant ROLE RESOURCE, thus indirectly granting the privilege set of this role
    GRANT RESOURCE TO sales1 WITH ADMIN OPTION;

    -- User sales delegates the system privilege SELECT ANY TABLE to user sales1
    conn sales/sales
    GRANT SELECT ANY TABLE TO sales1;
    ```
    
    > **Note**: 
    >
    > Specifying WITH ADMIN OPTION is invalid when granting SYSDBA and SYSOPER roles.

## Privilege Revocation

Revoking privileges granted to users and roles takes effect immediately. If the target role has been authorized to a user, the user's privileges are also revoked immediately.

Revoking a role granted to a user takes effect the next time the user connects. For example, if role B is revoked from user A, user A still possesses role B during the existing connection, but loses role B when user A logs in again.

In ISC Distributed Cluster Deployment, roles cannot grant roles, so there is no role revocation for roles.

> **Caution**: 
>
> Revoking privileges may cause certain database operations to run without privileges; please be cautious when performing this operation.

### Prerequisites 

- Users with the GRANT ANY PRIVILEGE system privilege can revoke grants of system privileges.

- Users with the GRANT ANY OBJECT PRIVILEGE object privilege can revoke grants of object privileges.

- Users with the GRANT ANY ROLE system privilege can revoke roles' grants.

- Users with the SECURITY_ADMIN security admin role can revoke any privilege/role.

- Ordinary users can only revoke privileges they have granted; even if privilege transfer across users has been achieved through the WITH GRANT|ADMIN OPTION statement, they cannot revoke across users.

### Operational Steps

1. Log in to YashanDB using a user satisfying the revocation conditions.

2. Execute the [REVOKE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/REVOKE) statement to initiate revocation on another user/role.

    ```sql
    -- Revoke the SELECT privilege on table area from user sales
    REVOKE SELECT ON area FROM sales;
    
    -- Revoke the RESOURCE role from user sales1
    REVOKE RESOURCE FROM sales1;

    -- User sales can revoke the system privilege SELECT ANY TABLE delegated to sales1
    conn sales/sales
    REVOKE SELECT ANY TABLE FROM sales1;
    ```
