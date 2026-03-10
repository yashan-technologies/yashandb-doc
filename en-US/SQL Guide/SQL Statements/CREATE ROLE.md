General Description
----

CREATE ROLE is used to create a role. Role management is part of the privilege system. For the role management system of YashanDB, please refer to the product security manual [Role Management](../../All Manuals/Product Security/Data Access Control/Privilege and Role Management/Roles).

YashanDB includes the following two types of roles:

*   System built-in roles: such as the DBA role and the PUBLIC role.
*   Ordinary roles: ordinary roles created by this statement.

Statement Definition
----

**create role::=**

```ebnf+diagram
syntax::= CREATE ROLE role [SLOT slot_id]
```

### 1. role

This statement is used to specify the name of the role to be created. It cannot be omitted and must comply with YashanDB's [Object Naming Convention](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

***Example***

```sql
CREATE ROLE rolename;
```

### 2. SLOT

If a new version of YashanDB adds object privileges or system privileges, manual creation of corresponding privileges after upgrading the database to that version is required, and the privilege name and corresponding ID must be specified based on this statement. **This should be done under the guidance of the original factory engineer.**

***Example***

```sql
CREATE ROLE privilegename SLOT 10;
```
