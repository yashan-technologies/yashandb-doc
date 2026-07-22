General Description
----

CREATE USER is used to create a new database user. For the user management system of YashanDB, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/Managing Users).


In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), if this statement is used while connected to the CDB root, it indicates creating a global user.


Statement Definition
----

**create user::=**

```ebnf
= CREATE USER user_name 
(IDENTIFIED BY [VALUES] password
| DEFAULT TABLESPACE tablespace
| DEFAULT TABLESPACE SET tablespace_set
| TEMPORARY TABLESPACE tablespace
| LOCAL TEMPORARY TABLESPACE tablespace
| PROFILE profilename
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE
| quota_clause {" " quota_clause})
{" " 
(IDENTIFIED BY [VALUES] password
| DEFAULT TABLESPACE tablespace
| DEFAULT TABLESPACE SET tablespace_set
| TEMPORARY TABLESPACE tablespace
| LOCAL TEMPORARY TABLESPACE tablespace
| PROFILE profilename
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE
| quota_clause {" " quota_clause})
} 
[CONTAINER "=" (CURRENT|ALL)].
```

**[quota\_clause](#quotaclause)::=**

```ebnf
= 
((QUOTA | FLOOR) (size_clause | UNLIMITED) ON tablespace )
{" " ((QUOTA | FLOOR) (size_clause | UNLIMITED) ON tablespace )}.
```

### user\_name

This statement is used to specify the name of the user to be created. It is mandatory and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

If the username contains special characters (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes to ensure successful parsing. The same applies to subsequent operations that require specifying the username, such as ALTER USER, GRANT, and REVOKE.

If enclosed in double quotes, the letters are case-sensitive.


In a CDB, global user names must start with a prefix. The default prefix is C##, and the prefix value can be configured through the COMMON_USER_PREFIX parameter before creating global roles, global users, and global profiles. Local user names can never start with C## or custom-specified prefixes.  


### VALUES

This statement is used to specify the password for creating the user in encrypted format. It is optional; if omitted, the password will be created in plaintext.

YashanDB's password policy requires encrypted transmission and storage, meaning the server stores the encrypted password. In certain specific scenarios (such as database migration), passwords can be created by directly specifying the encrypted format, which does not affect the user's ability to log in with plaintext passwords.

### IDENTIFIED BY password

This statement is used to specify the password for the user being created. It is optional; if omitted, the default is no password.

<span id="password" name="password"></span>

The password must meet the following rules:

* Cannot be specified as NULL or ''.
* Can be a combination of numbers, letters, and special symbols.
* Password characters cannot include double quotes.
* If the password contains special symbols (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes to ensure successful parsing.
* Case-sensitive.
* The password string length must not exceed 64.
* Must comply with the [password strength](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy.md#password_complexity) and [password policy](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy.md#password_policy) requirements.

> **Note**: 
>
> Users without a password cannot connect to YashanDB. It is recommended to specify a password when creating a new user.

When specifying VALUES, the password content is encrypted and does not follow the above rules, but must conform to the following basic format requirements; otherwise, user creation will fail with error YAS-04289:

- Must start with `S:`.
- Total length must be 86 bytes.
- After `S:`, only numbers and uppercase letters are allowed.

**Special Handling of @ and /**

YashanDB allows the inclusion of special characters like `@` and `/` in passwords. When logging in with passwords containing `@` or `/`, the password strings must also be enclosed in double quotes.

In the Linux OS command line, double quotes are special characters and must be escaped with `\` or enclosed in single quotes.

***Example***

```sql
-- Create two users, sales1 and sales2, where sales2's password contains @ and /
CREATE USER sales1 IDENTIFIED BY "1%2";
CREATE USER sales2 IDENTIFIED BY "1@2/";

-- At least grant the CREATE SESSION privilege to the user to enable database connection
GRANT CREATE SESSION TO sales1;
GRANT CREATE SESSION TO sales2;

-- Password input format for SQL client connection
SQL> conn sales1/1%2
SQL> conn sales2/"1@2/"

-- Password input format for command line connection
$ yasql sales1/1%2
$ yasql sales2/\"1@2/\"
```

### DEFAULT TABLESPACE

This statement is used to specify the default tablespace for the user being created. It is optional, and if omitted, the default is the DEFAULT tablespace created with the database.

***Example***

```sql
CREATE USER sales3 IDENTIFIED BY 123 DEFAULT TABLESPACE users;
```

### DEFAULT TABLESPACE SET

This statement is used to specify the default tablespace set for the user. The corresponding tablespace set must exist. It is optional, and if omitted, the default is the USERS tablespace set.

This statement is applicable only for ISC Distributed Cluster Deployment.

***Example*** for ISC Distributed Cluster Deployment

```sql
CREATE TABLESPACE SET sales_tss ON USERS MAXSIZE 1G;
CREATE USER sales123 IDENTIFIED BY 123 DEFAULT TABLESPACE SET sales_tss;
```

### TEMPORARY TABLESPACE

This statement is used to specify the default TEMP tablespace for the user being created. It is optional, and if omitted, the default is the TEMP tablespace created with the database.

***Example***

```sql
CREATE USER sales3 IDENTIFIED BY 123 TEMPORARY TABLESPACE temp;
```

### LOCAL TEMPORARY TABLESPACE

This statement is used to specify the user's default local TEMP tablespace. It is optional, and if omitted, the default is empty, in which case the user will use the TEMP tablespace for creating temporary tables.

If both local TEMP tablespace and TEMP tablespace are specified, the local TEMP tablespace will take precedence.

This functionality is not available in ISC Distributed Cluster Deployment / Standalone Deployment.

***Example*** for YAC/Distributed Cluster Deployment

```sql
CREATE USER sales3 IDENTIFIED BY 123 LOCAL TEMPORARY TABLESPACE local_temp;
```

<span id="profile" name="profile"></span>

### PROFILE profilename

This statement is used to assign a corresponding profile to the user being created. The dba_profiles view can be used to check all profiles and their contents. For creating a new profile, please refer to [CREATE PROFILE](CREATE PROFILE).

This statement is optional, and if omitted, the default system profile will apply.

***Example***

```sql
CREATE USER sales5 IDENTIFIED BY 123 PROFILE default;
```

### ACCOUNT (LOCK|UNLOCK)

This statement is used to specify whether to lock the user during creation. If locked, the user will be unable to log in.

This statement is optional; if omitted, the default is to not lock the user.

***Example***

```sql
CREATE USER sale4 IDENTIFIED BY 123 ACCOUNT LOCK;
```

### PASSWORD EXPIRE

This statement is used to directly expire the initial password of the user upon creation. The user must change the password on first login to the database.

***Example***

```sql
CREATE USER sale8 IDENTIFIED BY 123 PASSWORD EXPIRE;
```

<span id="quotaclause" name="quotaclause"></span>

### quota\_clause

This statement is used to specify the tablespace quota for the user. It is optional, and this statement does not apply to ISC Distributed Cluster Deployment.

Tablespace quotas include upper and lower limits. The upper limit refers to the maximum space a user can use in the corresponding tablespace, while the lower limit indicates the minimum space that must be reserved for the user.

If no tablespace quota or if both upper and lower limits are specified as UNLIMITED, the user can freely use the tablespace.

The configuration rules for tablespace quotas are as follows:

- If the specified high value or lower limit is not a multiple of a single BLOCK size, it will be automatically rounded up to the nearest integer multiple of the BLOCK size.
- The lower limit must not be greater than the high value.
- To configure a lower limit for a user on a certain tablespace, ensure that the tablespace has sufficient reserved space; otherwise, an error will be reported.
- No lower limit constraints can be configured for the SYSTEM tablespace.
- The sys user and users with DBA roles are not subject to the upper limit constraints of tablespace quotas, and tablespace quota constraints cannot be configured for sys users (even if the statement executes successfully, it will not take effect).

#### QUOTA(size_clause|UNLIMITED)

The QUOTA keyword is used to specify the upper limit of the tablespace quota for the user. It is optional.

If omitted or specified as UNLIMITED, it indicates no upper usage limit constraint.

The high value must be a positive number not exceeding 2T, expressed in bytes, and can be an Arabic numeral (e.g., 1, 20, or 100) or contain unit values (e.g., 2K, 3M, 4G, or 1T).

#### FLOOR(size_clause|UNLIMITED)

The FLOOR keyword is used to specify the lower limit for the tablespace quota of the user. It is optional.

If omitted or specified as UNLIMITED, it indicates no reserved space.

The lower limit must be a positive number not exceeding 2T, expressed in bytes, and can be an Arabic numeral (e.g., 1, 20, or 100) or contain unit values (e.g., 2K, 3M, 4G, or 1T).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE USER sale5 IDENTIFIED BY u1 QUOTA 100M FLOOR 10000 ON tablespace1;

CREATE USER sale6 IDENTIFIED BY u2 QUOTA UNLIMITED ON tablespace2;

CREATE USER sale7 IDENTIFIED BY u3 FLOOR 20M ON tablespace3 QUOTA 1G ON tablespace4;
```

### CONTAINER



This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), used to specify the container scope for the current operation, which can be set to take effect globally or locally within the current container. It can be omitted.

CONTAINER attribute optional values are as follows:

- CURRENT: Indicates effectiveness only in the currently connected container locally. When directly connecting to PDB, only this value can be specified or completely omitted, with the same effect.

- ALL: Indicates global effectiveness. Only available when connecting to the CDB root and defaults to this value when omitted.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE USER c##sale1 IDENTIFIED BY s1_password CONTAINER = all;
```
