General Description
----

DROP ROLE is used to remove an existing role. When a role is deleted, the system automatically revokes the role from any users and roles that have been granted that role.

Built-in system roles cannot be deleted.

In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), deleting global roles must be executed by connecting to the CDB root. 

Statement Definition
----

**drop user::=**

```ebnf+diagram
syntax::= DROP ROLE role
```

### 1. role

This clause specifies the name of the role to be deleted.

***Example***

```sql
DROP ROLE rolename;
```
