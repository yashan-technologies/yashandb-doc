## General Description

The ALTER DATABASE LINK statement is used to modify the information of a database link, allowing the modification of the username or password for the remote database.

Users who modify a public database link must have the ALTER PUBLIC DATABASE LINK system privilege, while users modifying a non-public database link must have the ALTER DATABASE LINK system privilege.

## Statement Definition

**[alter database link](#alterdatabaselink)::=**

```ebnf
= ALTER [PUBLIC] DATABASE LINK dblink_name [CONNECT TO username IDENTIFIED BY pwd_clause].
```

**[pwd\_clause](#pwdclause)::=**

```ebnf
= plaintext_password | VALUES ['"'] ciphertext_password ['"'].
```

<span id="alterdatabaselink" name="alterdatabaselink"></span>

### PUBLIC

The keyword must be specified only when modifying a public database link.

### dblink\_name

The name of the existing database link.

### username

This statement is used to specify the username for accessing the remote database.

<span id="pwdclause" name="pwdclause"></span>

### plaintext_password

This statement is used to specify the plaintext password for the user accessing the remote database.

### VALUES ciphertext_password

This statement is used to specify the ciphertext password for the user accessing the remote database.

***Example***

```sql
-- Database link between YashanDB and YashanDB
ALTER DATABASE LINK dblink_yashan CONNECT TO sales identified BY sales;

-- Database link between YashanDB and Oracle
ALTER PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS identified BY REGRESS;
```
