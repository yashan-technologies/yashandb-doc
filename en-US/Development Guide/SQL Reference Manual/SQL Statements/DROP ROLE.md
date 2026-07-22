General Description
----

DROP ROLE is used to remove an existing role. When a role is deleted, the system automatically revokes the role from any users and roles that have been granted that role.

Built-in system roles cannot be deleted.

Statement Definition
----

**drop user::=**

```ebnf
= DROP ROLE role.
```

### role

This clause specifies the name of the role to be deleted.

***Example***

```sql
DROP ROLE rolename;
```
