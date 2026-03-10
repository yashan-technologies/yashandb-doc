General Description
----

CREATE ROLE is used to create a role. 

Role management is part of the privilege system. For the role management system of YashanDB, please refer to [Role Management](../../All Manuals/Product Security/Data Access Control/Privilege and Role Management/Roles).YashanDB includes the following two types of roles:

*   System built-in roles: such as the DBA role and the PUBLIC role.
*   Ordinary roles: ordinary roles created by this statement.


In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), if this statement is used while connected to the CDB root, it indicates creating a global role.


Statement Definition
----

**create role::=**

```ebnf+diagram
syntax::= CREATE ROLE role [SLOT slot_id] [container "=" (CURRENT|ALL)]
```

### 1. role

This statement is used to specify the name of the role to be created. It cannot be omitted and must comply with YashanDB's [Object Naming Convention](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).


In a CDB, global role names must start with a prefix. The default prefix is C##, and the prefix value can be configured through the COMMON_USER_PREFIX parameter before creating global roles, global users, and global profiles. Local role names can never start with C## or custom-specified prefixes.  


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

### 3. container



This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), used to specify the container scope for the current operation, which can be set to take effect globally or locally within the current container. It can be omitted.

CONTAINER attribute optional values are as follows:

- CURRENT: Indicates effectiveness only in the currently connected container locally. When directly connecting to PDB, only this value can be specified or completely omitted, with the same effect.

- ALL: Indicates global effectiveness. Only available when connecting to the CDB root and defaults to this value when omitted.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE ROLE c##manager container = ALL;
```
