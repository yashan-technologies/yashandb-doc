General Description
----

The CREATE DATABASE LINK statement is used to create a database link object. The local current user can access the physical table data of a certain user in a remote database through the database link.

The user who creates a public database link must have the CREATE PUBLIC DATABASE LINK system privilege, while the user who creates a non-public database link must have the CREATE DATABASE LINK system privilege.

Depending on the structure of the database system, there are two types of database links supported:

- Homogeneous database link: Database link between YashanDB and YashanDB

- Heterogeneous database link: Database link between YashanDB and Oracle, which requires [heterogeneous database link configuration](../General SQL Syntax/dblink/Example of DBLINK)

Statement Definition
----

**[create database link](#createdatabaselink)::=**

```ebnf
= CREATE [PUBLIC] DATABASE LINK dblink_name
[connect_clause] [USING connect_string].
```

**[connect\_clause](#connectclause)::=**

```ebnf
= CONNECT TO username IDENTIFIED BY pwd_clause.
```

**[pwd\_clause](#pwdclause)::=**

```ebnf
= plaintext_password | VALUES ['"'] ciphertext_password ['"'].
```

**[connect\_string](#connectstring)::=**

```ebnf
= [database_type ':'] url ['/' db_name].
```

**[database\_type](#databasetype)::=**

```ebnf
= [YASHAN | ORACLE].
```

<span id="createdatabaselink" name="createdatabaselink"></span>

### PUBLIC

This statement establishes a public database link, which is visible to all database users.

### dblink\_name

This statement is used to specify the name of the created database link, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="connectclause" name="connectclause"></span>

### username

This statement is used to specify the username to access the remote database.

<span id="pwdclause" name="pwdclause"></span>

### plaintext_password

This statement is used to specify the plaintext password of the user accessing the remote database.

### VALUES ciphertext_password

This statement is used to specify the ciphertext password of the user accessing the remote database.

<span id="connectstring" name="connectstring"></span>

### connect_string

This statement is used to specify the connection string information for connecting to the remote database.

The correctness of the connection string is not validated when creating the DATABASE LINK (validation occurs when it is used), and the ALTER DATABASE LINK statement cannot modify the connection string. Please ensure it is set correctly; otherwise, it can only be deleted and recreated.

<span id="databasetype" name="databasetype"></span>

#### database\_type

This statement is used to specify the remote database system or standard interface label name, supporting identification of Yashan and Oracle; the default is Yashan if omitted.

#### url

This statement is used to specify the connection address of the remote database, such as 192.168.1.2:1688.

If the remote database is YashanDB, for the format and description of the connection address, please refer to the URL in [Server Address](../../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Connecting to YashanDB.md#URL).

#### db\_name

This statement is used to specify the name of the remote Oracle database.

If the remote database is YashanDB, the database name cannot be specified.

***Example***

```sql
-- Database link between YashanDB and YashanDB
CREATE DATABASE LINK dblink_yashan CONNECT TO sales identified BY sales USING '192.168.1.2:1688';

-- Database link between YashanDB and Oracle, visible to all users
CREATE PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS identified BY REGRESS USING 'oracle:192.168.1.2:1521/orcl';
```
