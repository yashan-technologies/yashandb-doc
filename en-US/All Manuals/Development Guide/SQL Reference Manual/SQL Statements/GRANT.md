Generic Description
----

GRANT is used to grant privileges to a user or role. The granted items include [system privilege SYSTEM PRIVILEGE](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges), [object privilege OBJECT PRIVILEGE](../../../Product Security/Data Access Control/Privilege and Role Management/Object Privileges), and [role ROLE](../../../Product Security/Data Access Control/Privilege and Role Management/Roles).

In a single GRANT statement, system-level privileges and roles can be granted to a user simultaneously; mixed granting of object-level privileges with system-level privileges or roles is not allowed.

Privileges granted to users take effect immediately.

Privileges granted to roles take effect immediately, and if the role has been granted to a user, that user's privileges also take effect immediately.

Privileges granted to users or roles take effect when the user logs in next. For example, if role B is granted to user A, user A will not possess role B before logging in again, but will have it upon re-login.

For specific details on the privilege system management of YashanDB, please refer to [Privilege and Role Management](../../../Product Security/Data Access Control/Privilege and Role Management/00Privilege and Role Management).

Statement Definition
----

**grant::=**

```ebnf+diagram
syntax::= grant_system_privilege|grant_object_privilege|grant_role
```

**[grant\_system\_privilege](#grant_system_privilege)::=**

```ebnf+diagram
syntax::= GRANT (((system_privilege) {"," (system_privilege)}) | (ALL PRIVILEGES)) TO ((user_name [WITH ADMIN OPTION])|role)
```

**[grant\_object\_privilege](#grant_object_privilege)::=**

```ebnf+diagram
syntax::= GRANT (((object_privilege) {"," (object_privilege)}) | (ALL PRIVILEGES)) ON [schema "."] object_name TO ((user_name [WITH GRANT OPTION])|role)
```

**[grant_role](#grant_role)::=**

```ebnf+diagram
syntax::= GRANT ((role) {"," (role)}) TO ((user_name [WITH ADMIN OPTION])|role)
```

<span id="grant_system_privilege" name="grant_system_privilege" class="yaslink"></span>

### 1. grant\_system\_privilege

This statement is used to grant system privileges to a user or role.

The requirements for the user executing this statement (the grantor) are as follows:

- If the three-right separation functionality is enabled, the user must have the SECURITY_ADMIN security administrator role.

- If the three-right separation functionality is disabled, the user must have either the SECURITY_ADMIN security administrator role or the DBA role.

- A normal user who has been granted the corresponding system privilege and simultaneously specified the WITH ADMIN OPTION clause.

- A normal user who has the GRANT ANY PRIVILEGE system privilege.

#### 1.1. system\_privilege

This refers to the name of the system privileges being granted. A single statement can grant up to 300 system privileges and roles, separated by commas. For a list of system privileges, please consult [System Privileges](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges).

If ALL PRIVILEGES is specified, it means that all system privileges are granted.

#### 1.2. user\_name|role

The grantee, which is the name of the user or role.

#### 1.3. WITH ADMIN OPTION

When granting a system privilege to a certain user, specifying this clause indicates that the user has the management privilege for this privilege, meaning they can grant this system privilege to other users or roles.

***Example***

```sql
-- Grant the following privileges to user sales1 under user sales, and specify WITH ADMIN OPTION
GRANT ALTER SYSTEM, CREATE USER, ALTER USER, DROP USER TO sales1 WITH ADMIN OPTION;
 
-- At this point, sales1 can grant the above privileges to other users
conn sales1/1%2
GRANT ALTER SYSTEM TO sales2;
```

<span id="grant_object_privilege" name="grant_object_privilege" class="yaslink"></span>

### 2. grant\_object\_privilege

This statement is used to grant object privileges to a user or role.

It is recommended that the object privileges of a certain object be managed by its owning user. Additionally, the following users may execute this statement to manage object privileges:

- If the three-right separation functionality is enabled, the user must have the SECURITY_ADMIN security administrator role.

- If the three-right separation functionality is disabled, the user must have either the SECURITY_ADMIN security administrator role or DBA role.

- A normal user who has been granted the corresponding privilege and simultaneously specified the WITH GRANT OPTION clause.

- A normal user who holds the GRANT ANY OBJECT PRIVILEGE system privilege.

#### 2.1. object\_privilege

This refers to the name of the object privileges being granted. A single statement can grant up to 300 privileges, separated by commas. For a list of object privileges, please consult [OBJECT PRIVILEGE](../../../Product Security/Data Access Control/Privilege and Role Management/Object Privileges).

If ALL PRIVILEGES is specified, it means that all object privileges are granted.

#### 2.2. object\_name

The name of the object, which can be specified as an existing table, stored procedure, advanced package, UDF, or UDT. Alias names are not allowed. 

#### 2.3. user\_name|role

The grantee, which is the name of the user or role.

#### 2.4. WITH GRANT OPTION

When granting object privileges to a certain user, specifying this clause indicates that the user can further grant these object privileges to other users or roles.

***Example***

```sql
-- Grant SELECT and UPDATE privileges on the area table to user sales1 and specify WITH GRANT OPTION under user sales
GRANT SELECT,UPDATE ON area TO sales1 WITH GRANT OPTION;

-- At this point, sales1 will have the SELECT and UPDATE privileges on the area table and can grant these privileges to other users
conn sales1/1%2
GRANT SELECT ON sales.area TO sales2;
```

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Grant EXECUTE privilege on UDT udt_object to user sales1 under user sales
GRANT EXECUTE ON udt_object TO sales1;
```

<span id="grant_role" name="grant_role" class="yaslink"></span>

### 3. grant\_role

This statement is used to grant roles to a user or role.

In ISC Distributed Cluster Deployment, it is not possible to grant roles to roles.

The requirements for the user executing this statement (the grantor) are as follows:

- If the three-right separation functionality is enabled, the user must have the SECURITY_ADMIN security administrator role.

- If the three-right separation functionality is disabled, the user must have either the SECURITY_ADMIN security administrator role or DBA role.

- A normal user who has been granted a role and simultaneously specified the WITH ADMIN OPTION clause.

- A normal user who holds the GRANT ANY ROLE system privilege.

#### 3.1. role

This refers to the name of the roles being granted. In ISC Distributed Cluster Deployment, a single statement can grant up to 8 roles; in other deployment types, a single statement can grant up to 300 system privileges and roles, separated by commas. For all roles, please refer to the [DBA_ROLES](../../../Reference Manual/System Views/DBA Views/DBA_ROLES) view.

#### 3.2. user\_name|role

The grantee, which is the name of the user or role.

#### 3.3. WITH ADMIN OPTION

When granting a role to a certain user, specifying this clause indicates that the user can grant this role to other users or roles.

> **Note**: 
>
> Specifying WITH ADMIN OPTION is ineffective when granting SYSDBA and SYSOPER roles.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Create role ya_rol1 under user sales and grant SELECT privilege on the area table to it 
CREATE ROLE ya_rol1;
GRANT SELECT ON area TO ya_rol1;
 
-- Grant the ya_rol1 role privileges to user sales1; thus, after sales1 reconnects, it will obtain the SELECT privilege on sales.area. This operation requires the sales user to have the GRANT ANY ROLE privilege.
-- Since the keyword WITH ADMIN OPTION is not specified, sales1 cannot grant the role ya_rol1 to other users.
GRANT ya_rol1 TO sales1;

-- Create role ya_rol2 under user sales and grant CREATE TABLE privilege to it, grant ya_rol2 to ya_rol1; since ya_rol1 has already been granted to user sales1, after sales1 reconnects, it will possess the ya_rol2 role.
CREATE ROLE ya_rol2;
GRANT CREATE TABLE TO ya_rol2;
GRANT ya_rol2 TO ya_rol1;

-- Re-grant the ya_rol1 role privileges to user sales1 and specify the keyword WITH ADMIN OPTION; at this point, user sales1 can grant the ya_rol1 role to other users.
REVOKE ya_rol1 FROM sales1;
GRANT ya_rol1 TO sales1 WITH ADMIN OPTION;
```
