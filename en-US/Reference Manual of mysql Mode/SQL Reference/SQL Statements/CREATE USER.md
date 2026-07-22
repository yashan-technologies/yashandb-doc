## General Description

CREATE USER is used to create a new database user. The default created is a regular user. For YashanDB's user management system, please refer to the Product Security Manual [User Management](../../Product Security/Managing Users).

## Statement Definition

**create user::=**

```ebnf
= CREATE USER user_name 
(IDENTIFIED BY password
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE).
```

### user\_name

This statement is used to specify the name of the user to be created. It is mandatory and must comply with YashanDB's [Object Naming Conventions](../Basic SQL Elements/Identifiers), with case sensitivity for English letters.

If the username contains special symbols (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes to ensure successful parsing. Double quotes must also be used in subsequent operations that require specifying the username, such as ALTER USER, GRANT, REVOKE, etc.

Due to the case sensitivity difference between yashan mode and mysql mode, when connecting to users created in mysql mode through the yasql tool, the username in the connection string needs to be enclosed in double quotes, but when connecting through MySQL Client, double quotes are not required.

### IDENTIFIED BY password

This statement is used to specify the password for the created user. It is optional; if omitted, the default is no password.

This statement must meet the following rules:

*   It cannot be specified as NULL or ''.
*   It can be a combination of numbers, letters, and special symbols.
*   Password characters cannot be double quotes.
*   If the password contains special symbols (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be enclosed in double quotes to ensure successful parsing.
*   It is case sensitive.
*   The password string length must not exceed 64.
*   It must comply with [Password Strength](../../Product Security/Password Settings) requirements.

> **Note**: 
>
> Users without a password cannot connect to YashanDB. It is recommended to specify a password for the user when creating a new user.

**Special Handling of @ and /**

YashanDB allows the inclusion of special symbols such as `@` and `/` in the password. When entering the password for login, strings containing `@` or `/` must also be enclosed in double quotes.

In the Linux OS command line, double quotes are special characters. Their use requires escaping with `\` or wrapping in single quotes.

***Example*** for  Heap tables

```sql
-- Create two users, sales1 and sales2, where the password for sales2 contains @ and /
CREATE USER sales1 IDENTIFIED BY 'your_Password1';
CREATE USER sales2 IDENTIFIED BY "your@Password1";

-- Format of the password to be input when connecting via SQL client
SQL> conn "sales1"/your_Password1
SQL> conn "sales2"/"your@Password1"

-- Format of the password to be input when connecting via yasql command line
$ yasql \"sales1\"/your_Password1
$ yasql \"sales2\"/\"your@Password1\"

-- Format of the password to be input when connecting via MySQL Client, and the default port is 1690 (Standalone (primary/standby) deployment) or 1691 (YAC deployment)
$ mysql -h ipaddr -P 1690 -u sales1 -p
your_Password1
```

### ACCOUNT (LOCK|UNLOCK)

This statement is used to specify whether to lock the user at the time of creation. If locked, the user will be unable to log in.

This statement is optional; if omitted, the default is to not lock the user.

***Example*** for  Heap tables

```sql
CREATE USER sales3 IDENTIFIED BY 'your_Password3' ACCOUNT LOCK;
```

### PASSWORD EXPIRE

This statement is used to invalidate the initial password upon user creation. The user must change the password upon first login to the database.

***Example*** for  Heap tables

```sql
CREATE USER sales4 IDENTIFIED BY 'your_Password4' PASSWORD EXPIRE; 
```
