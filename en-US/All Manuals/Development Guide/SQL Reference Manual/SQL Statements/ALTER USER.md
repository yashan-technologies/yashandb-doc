General Description
----

ALTER USER is used to modify user attributes, including password, default tablespace, etc. For the user management system of YashanDB, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/Managing Users).

In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), it is recommended to connect to the CDB root when modifying global user attributes. Updating passwords and expiring passwords must be performed by connecting to the CDB root.

Statement Definition
----

**alter user::=**

```ebnf+diagram
syntax::= ALTER USER user_name 
(IDENTIFIED BY [VALUES] password
|DEFAULT TABLESPACE tablespace
|DEFAULT TABLESPACE SET tablespace_set
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK)
|PROFILE profilename
|(quota_clause) {" " (quota_clause)})
{" " 
(IDENTIFIED BY [VALUES] password
|DEFAULT TABLESPACE tablespace
|DEFAULT TABLESPACE SET tablespace_set
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK)
|PROFILE profilename
|(quota_clause) {" " (quota_clause)})}
[CONTAINER "=" (CURRENT|ALL)]
```

### 1. user\_name

An already existing username.

If the username contains special characters (except for underscore), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes to ensure successful parsing.

### 2. VALUES

This statement is used to specify that the password is modified in encrypted form. If omitted, the password will be modified in plain text.

YashanDB's password policy is encrypted transmission and encrypted storage, meaning the server stores the password in encrypted form. In certain specific scenarios (e.g., database migration), the password can be modified by specifying the encrypted form directly, which does not affect the user's ability to log in with plain text password.

### 3. password

The new password specified for the user, which must comply with the password requirements outlined in [CREATE USER](CREATE USER).

### 4. tablespace

Specifies the default tablespace for the user.

### 5. tablespace\_set

Specifies the default tablespace set for the user, using the same rules as [CREATE USER](CREATE USER).

### 6. PASSWORD EXPIRE

Invalidates the user's password. Once the password is invalidated, the user cannot log in and must reset the password to log in again.

### 7. ACCOUNT (LOCK|UNLOCK)

Locks/unlocks the user. When locked, the user will not be able to log in.

For global users in a CDB, if connecting to the CDB root for lock operations, only global locking can be performed, i.e., connect to the CDB root and specify CONTAINER = ALL.

<span id="profile" name="profile" class="yaslink"></span>

### 8. PROFILE profilename

Specifies a new profile for the user. All profiles and their contents can be queried through the DBA_PROFILES view. For creating a new profile, please refer to [CREATE PROFILE](CREATE PROFILE).

### 9. quota\_clause

The usage rules are the same as those in the [quota_clause](CREATE USER.html#quotaclause) in CREATE USER.

### 10. CONTAINER



This statement is only applicable to CDBs (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), used to specify the container scope for the current operation, which can be set to take effect globally or locally within the current container. When omitted, it defaults to local effectiveness.

CONTAINER attribute optional values are as follows:

- CURRENT: Indicates local effectiveness only in the currently connected container, which can only be specified when directly connecting to a PDB.

- ALL: Indicates global effectiveness, which is only available when connecting to the CDB root.



***Example***

```sql
ALTER USER sales1 IDENTIFIED BY "23%ad1";
  
ALTER USER sales2 DEFAULT TABLESPACE users;

ALTER USER sales3 PASSWORD EXPIRE;

ALTER USER sales3 ACCOUNT UNLOCK;

ALTER USER sales3 PASSWORD EXPIRE ACCOUNT LOCK;

ALTER USER sales3 PROFILE DEFAULT;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER USER sales3 quota 100M ON tablespace1;

ALTER USER c##sale1 PROFILE c##prof_all;
```
