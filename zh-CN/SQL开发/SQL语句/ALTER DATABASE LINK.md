## 通用描述

ALTER DATABASE LINK语句用于修改数据库链接的信息，可以修改远程数据库的用户名或密码。

修改公有数据库链接的用户须拥有ALTER PUBLIC DATABASE LINK系统权限，修改非公有数据库链接用户须拥有ALTER DATABASE LINK系统权限。

## 语句定义

**[alter database link](#alterdatabaselink)::=**

```ebnf+diagram
syntax::= ALTER [PUBLIC] DATABASE LINK dblink_name [CONNECT TO username IDENTIFIED BY pwd_clause]
```

**[pwd\_clause](#pwdclause)::=**

```ebnf+diagram
syntax::= plaintext_password | VALUES ['"'] ciphertext_password ['"']
```

<span id="alterdatabaselink" name="alterdatabaselink" class="yaslink"></span>

### 1. PUBLIC

仅当修改公有数据库链接时，需指定该关键字。

### 2. dblink\_name

已存在的数据库链接名称。

### 3. username

该语句用于指定访问远端数据库的用户名。

<span id="pwdclause" name="pwdclause" class="yaslink"></span>

### 4. plaintext\_password

该语句用于指定访问远端数据库的用户明文密码。

### 5. VALUES ciphertext\_password

该语句用于指定访问远端数据库的用户密文密码。

示例

```sql
-- YashanDB与YashanDB的数据库链接
ALTER DATABASE LINK dblink_yashan CONNECT TO sales IDENTIFIED BY sales;

-- YashanDB与Oracle的数据库链接
ALTER PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS IDENTIFIED BY REGRESS;
```
