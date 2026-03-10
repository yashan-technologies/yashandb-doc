## General Description

REVOKE is used to revoke the privilege granted to a specific user. The revocable contents include [system privilege SYSTEM PRIVILEGE](../../Product Security/Privilege Management/System Privileges), [schema privilege SCHEMA PRIVILEGE](../../Product Security/Privilege Management/Schema Privileges), and [object privilege OBJECT PRIVILEGE](../../Product Security/Privilege Management/Object Privileges).

The revocation of the privilege granted to users takes effect immediately.

Regular users can only revoke the privileges they have granted. Even in scenarios where cross-user authorization is implemented using the WITH GRANT OPTION statement, they cannot revoke privileges across users.

## Statement Definition

**revoke::=**

```ebnf+diagram
syntax::= revoke_system_privilege|revoke_schema_privilege|revoke_object_privilege
```

**[revoke\_system\_privilege](#revoke_system_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((system_privilege)) {"," (system_privilege)} ON "*.*" FROM user_name
```

**[revoke\_schema\_privilege](#revoke_schema_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((schema_privilege)) {"," (schema_privilege)} ON [schema "."]"*"  FROM user
```

**[revoke\_object\_privilege](#revoke_object_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((object_privilege)) {"," (object_privilege)} ON [TABLE] [schema "."] table_name FROM user_name
```

<span id="revoke_system_privilege" name="revoke_system_privilege" class="yaslink"></span>

### revoke\_system\_privilege

This statement is used to revoke the specified system privileges from a user.

#### system\_privilege

The name of the system privilege to be revoked. Up to 300 system privileges and roles can be revoked in a single statement, separated by commas.

Query the MYSQL.USER, MYSQL.DB, or INFORMATION_SCHEMA.USER_PRIVILEGES views to understand the granting records of system privileges and confirm the privilege information that needs to be revoked.

#### user\_name

The user name.

<span id="revoke_schema_privilege" name="revoke_schema_privilege" class="yaslink"></span>

### revoke\_schema\_privilege

This statement is used to revoke the specified schema privileges from a user.

#### schema\_privilege

The name of the schema privilege to be revoked, separated by commas.

Schema-level privileges do not take effect for the sys schema.

Query the INFORMATION_SCHEMA.SCHEMA_PRIVILEGES view to understand the granting records of schema privileges and confirm the privilege information that needs to be revoked.

#### [schema.]*

The schema name, which must be specified as an existing schema.

#### user\_name

The user name.

<span id="revoke_object_privilege" name="revoke_object_privilege" class="yaslink"></span>

### revoke\_object\_privilege

This statement is used to revoke the specified object privileges from a user.

#### object\_privilege

The name of the object privilege to be revoked, separated by commas.

Query the MYSQL.TABLES_PRIV or INFORMATION_SCHEMA.TABLE_PRIVILEGES views to understand the granting records of object privileges and confirm the privilege information that needs to be revoked.

#### [schema.]table\_name

The name of the table, which must be specified as an existing table.

#### user\_name

The user name.