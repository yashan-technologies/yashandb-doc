General Description
----

REVOKE is used to revoke privileges granted to a user or role. The revocable items include [system privilege SYSTEM PRIVILEGE](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges), [object privilege OBJECT PRIVILEGE](../../../Product Security/Data Access Control/Privilege and Role Management/Object Privileges), and [role ROLE](../../../Product Security/Data Access Control/Privilege and Role Management/Roles).

In a single REVOKE statement, it supports revoking both system-level privileges and role privileges from a user simultaneously, but it does not allow mixing the revocation of object-level privileges with system-level privileges or roles.

Revoke operations on privileges granted to users take effect immediately.

Revoke operations on privileges granted to roles take effect immediately; if that role has been granted to a user, the revocation of the user's privileges also takes effect immediately.

Revocation of roles granted to users takes effect during the user's next connection, meaning if role B granted to user A is revoked, user A will still have role B until they log in again, at which point they will lose access to role B.

Ordinary users can only revoke privileges that they have granted. Even in cases of cross-user granting specified with WITH GRANT|ADMIN OPTION, they cannot revoke privileges from other users. Users with the SECURITY_ADMIN security administrator role can revoke any privilege.

YashanDB supports revoking roles granted to roles in standalone and YAC/Distributed Cluster Deployment; attempting this in ISC Distributed Cluster Deployment will result in an error.

In standalone or cluster deployment, a single REVOKE statement can revoke a maximum of 300 privileges; in ISC Distributed Cluster Deployment, if revoking system privileges or object privileges, a single REVOKE statement can revoke up to 300 privileges, while revoking role privileges allows a maximum of 8 role privileges per statement.

For specific details on the privilege management system in YashanDB, please refer to [Privilege and Role Management](../../../Product Security/Data Access Control/Privilege and Role Management/00Privilege and Role Management).

Statement Definition
----

**revoke::=**

```ebnf+diagram
syntax::= revoke_system_privilege|revoke_object_privilege|revoke_role
```

**[revoke\_system\_privilege](#revoke_system_privilege)::=**

```ebnf+diagram
syntax::= REVOKE (((system_privilege) {"," (system_privilege)})|ALL PRIVILEGES) FROM (user|role)
```

**[revoke\_object\_privilege](#revoke_object_privilege)::=**

```ebnf+diagram
syntax::= REVOKE (((object_privilege) {"," (object_privilege)})|ALL PRIVILEGES) ON [schema "."] object_name  FROM (user|role)
```

**revoke\_role::=**

```ebnf+diagram
syntax::= REVOKE ((role) {"," (role)}) FROM (user|role)
```

<span id="revoke_system_privilege" name="revoke_system_privilege" class="yaslink"></span>

### 1. revoke\_system\_privilege

This statement is used to revoke specified system privileges from a user/role.

The requirements for the user executing this statement (the one carrying out the revocation) are as follows:

- If the tripartite separation functionality is enabled, the user must possess the SECURITY_ADMIN security administrator role.

- If the tripartite separation functionality is disabled, the user must possess either the SECURITY_ADMIN security administrator role or DBA role.

- Ordinary users with the GRANT ANY PRIVILEGE privilege.

- If an ordinary user has obtained privilege for transfer via the WITH ADMIN OPTION statement, that user can revoke the privileges they have granted.

#### 1.1. system\_privilege

The names of the system privileges to be revoked, separated by commas.

If ALL PRIVILEGES is specified, it indicates revocation of all system privileges.

Check the DBA_SYS_PRIVS view to understand the authorization records for system privileges and to confirm the privileges to be revoked.

#### 1.2. user\_name|role

The object from which privileges are being revoked; must specify an already created user/role name.

***Example***

```sql
-- Revoke ALTER SYSTEM, CREATE USER, CREATE SESSION privileges and DBA role privilege from user sales1
REVOKE ALTER SYSTEM, CREATE USER, CREATE SESSION, DBA FROM sales1;
```

<span id="revoke_object_privilege" name="revoke_object_privilege" class="yaslink"></span>

### 2. revoke\_object\_privilege

This statement is used to revoke specified object privileges from a user/role.

The requirements for the user executing this statement (the one carrying out the revocation) are as follows:

- If the tripartite separation functionality is enabled, the user must possess the SECURITY_ADMIN security administrator role.

- If the tripartite separation functionality is disabled, the user must possess either the SECURITY_ADMIN security administrator role or DBA role.

- Ordinary users with the GRANT ANY OBJECT PRIVILEGE privilege.

- If an ordinary user has obtained privilege for transfer via the WITH GRANT OPTION statement, that user can revoke the privileges they have granted.

#### 2.1. object\_privilege

The names of the object privileges to be revoked, separated by commas.

If ALL PRIVILEGES is specified, it indicates revocation of all object privileges.

#### 2.2. object\_name

The name of the object; must specify an already created table or [UDT](../../PL Reference Manual/PL Objects/User-Defined Types).

#### 2.3. user\_name|role

The object from which privileges are being revoked; must specify an already created user/role name.

***Example***

```sql
-- Grant SELECT, INSERT, UPDATE, and DELETE privileges on table area to user sales1
GRANT SELECT, INSERT, UPDATE, DELETE ON area TO sales1 WITH GRANT OPTION;

-- Now sales1 has SELECT, INSERT, UPDATE, and DELETE privileges on table area and can grant these privileges to others
conn sales1/1%2
GRANT SELECT, INSERT, UPDATE, DELETE ON sales.area TO sales2;

-- Revoke the SELECT ON area privilege granted to sales1 by user sales
conn sales/sales
REVOKE SELECT, DELETE ON area FROM sales1;

-- The SELECT, INSERT, UPDATE, and DELETE privileges on sales.area granted to sales2 by sales1 can only be revoked by sales1 or the system user; this example revokes SELECT and DELETE privileges
conn sales1/1%2
REVOKE SELECT, DELETE ON sales.area FROM sales2;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Revoke EXECUTE ON udt_object privilege granted to sales1 by user sales
REVOKE EXECUTE ON udt_object FROM sales1;
```

<span id="revoke_role" name="revoke_role" class="yaslink"></span>

### 3. revoke\_role

This statement is used to revoke specified roles from a user/role.

The requirements for the user executing this statement (the one carrying out the revocation) are as follows:

- If the tripartite separation functionality is enabled, the user must possess the SECURITY_ADMIN security administrator role.

- If the tripartite separation functionality is disabled, the user must possess either the SECURITY_ADMIN security administrator role or DBA role.

- Ordinary users with the GRANT ANY ROLE privilege.

- If an ordinary user has obtained privilege for transfer via the WITH ADMIN OPTION statement, that user can revoke the privileges they have granted.

#### 3.1. role

The roles to be revoked; must specify an already created role name.

#### 3.2. user\_name|role

The object from which roles are being revoked; must specify an already created user/role name.

***Example***

```sql
-- Revoke ya_rol1 and ya_rol2 roles granted to sales1 by user sales
REVOKE ya_rol1, ya_rol2 FROM sales1;
```
