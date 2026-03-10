General Description
----

ALTER USER is used to modify user attributes, including password, default tablespace, etc. For the user management system of YashanDB, please refer to the Product Security Manual [User Management](../../../Product Security/Identity Identification and Authentication/User/00User).

Statement Definition
----

**alter user::=**

```ebnf+diagram
syntax::= ALTER USER user_name 
(IDENTIFIED BY password
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK))
```

### 1. user\_name

An existing username.

If the username contains special characters (excluding underscore), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes when specified to ensure successful parsing.

### 2. IDENTIFIED BY password

Specifies a new password for the user. The new password must adhere to the same password requirements as in [CREATE USER](CREATE USER).

### 3. PASSWORD EXPIRE

Invalidates the user's password. Once the password is invalidated, the user cannot log in and must reset the password to be able to log in.

### 4. ACCOUNT (LOCK|UNLOCK)

Locks/unlocks the user. Once locked, the user will not be able to log in.

***Example*** for Standalone Deployment Heap tables

```sql
ALTER USER sales1 IDENTIFIED BY 'your%Password1';

ALTER USER sales2 PASSWORD EXPIRE;

ALTER USER sales3 ACCOUNT UNLOCK;

ALTER USER sales4 PASSWORD EXPIRE ACCOUNT LOCK;
```
