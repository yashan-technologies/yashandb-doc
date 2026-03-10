After confirming that the authorized user is safe and trustworthy, and that their required privileges match the actual identity of the user, authorization can be granted.

Once it is confirmed that a user or role no longer needs a specific privilege, the privilege should be revoked in a timely manner. However, before executing the operation, it is necessary to carefully confirm that revoking the privilege will not affect normal business operations.

Users created under the mysql mode can be granted/revoked privileges that are the union of the privilege mechanisms of the Yashan mode and mysql mode ([privileges related to mysql mode](./00特权管理（mysql模式）) and [privileges related to yashan mode](../特权与角色管理（yashan模式）/00特权与角色管理（yashan模式）)), and privileges should be granted reasonably according to actual needs.

Privileges granted to users and revoked from users take effect immediately.

## Privilege Granting

1. Confirm the privilege requirements of the authorized object:

    - **"Principle of Least Privilege"**: Analyze and organize the minimal set of privileges required by the authorized object based on business needs. If an existing role carries the appropriate privilege set, direct granting of the corresponding role to the user can also be considered.

    - Whether privilege granting is needed: If it is necessary to grant a certain privilege/role, it must be specified with the WITH GRANT OPTION.

2. Log in to YashanDB using a user with sufficient privileges.

3. Execute the [GRANT (mysql mode)](../../../Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/GRANT) statement to initiate authorization for the target user. The optional privilege set can be found in [privileges related to mysql mode](00Privilege Management (mysql Mode)).

    ```sql
    -- Grant multiple system privileges
    GRANT CREATE TABLESPACE,CREATE USER,GRANT OPTION ON *.* TO SALES WITH GRANT OPTION;

    -- Grant all privileges in the specified schema to the SALES user
    GRANT ALL ON sales.* TO SALES WITH GRANT OPTION;
    ```

4. Execute the [GRANT (Yashan mode)](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/GRANT) statement to initiate authorization for the target user. The optional privilege set can be found in [privileges related to Yashan mode](../Privilege and Role Management (yashan Mode)/00Privilege and Role Management (yashan Mode)).

    ```sql
    -- Directly grant the system privilege CREATE ANY TABLE
    GRANT CREATE ANY TABLE TO sales;

    -- Directly grant the system privilege SELECT ANY TABLE, allowing the sales user to grant this privilege to other users/roles
    GRANT SELECT ANY TABLE TO sales WITH ADMIN OPTION;
    
    -- Directly grant SELECT privilege on the table area, allowing the sales user to grant this privilege to other users/roles
    GRANT SELECT ON area TO sales WITH GRANT OPTION;
    
    -- Grant the RESOURCE role, thus indirectly granting the privilege set of that role
    GRANT RESOURCE TO sales1 WITH ADMIN OPTION;

    -- The user sales grants the system privilege SELECT ANY TABLE to the user sales1
    conn sales/sales
    GRANT SELECT ANY TABLE TO sales1;
    ```
    
    > **Note**: 
    >
    > Specifying WITH ADMIN OPTION is ineffective when authorizing the SYSDBA and SYSOPER roles.

## Privilege Revocation

> **Caution**: 
>
> Revoking privileges may cause certain database operations to run without privileges; please proceed with caution.

1. Log in to YashanDB using a user with sufficient privileges.

2. Execute the [REVOKE](../../../Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/REVOKE) statement to initiate the revocation operation for another user/role.

    ```sql
    -- Revoke the SELECT privilege on the area table from user sales
    REVOKE SELECT ON area FROM sales;
    
    -- Revoke the RESOURCE role from user sales1
    REVOKE RESOURCE FROM sales1;

    -- User sales can revoke the system privilege SELECT ANY TABLE that was granted to sales1
    conn sales/sales
    REVOKE SELECT ANY TABLE FROM sales1;
    ```
