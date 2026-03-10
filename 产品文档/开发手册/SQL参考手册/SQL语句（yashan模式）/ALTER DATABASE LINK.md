## 通用描述

ALTER DATABASE LINK语句用于修改一个数据库链接对象的信息。当前登录的本地用户可以修改数据库链接的远程用户名和用户密码。

根据数据库系统的结构，支持两种数据库链接：

- 同构数据库链接：YashanDB与YashanDB的数据库链接
- 异构数据库链接：YashanDB与Oracle的数据库链接，需要进行[异构数据库链接配置](../../../数据库管理/基本数据库管理/异构数据库链接配置)

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

该语句修改一个公有数据库链接，对所有数据库用户可见。

修改公有数据库链接的用户须拥有ALTER PUBLIC DATABASE LINK系统权限，修改非公有数据库链接用户须拥有ALTER DATABASE LINK系统权限，否则返回错误。

示例

```sql
ALTER PUBLIC DATABASE LINK dblink_yashan;
```

### 2. dblink\_name

该语句用于指定创建的数据库链接的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 3. username

该语句用于指定访问远端数据库的用户名。

<span id="pwdclause" name="pwdclause" class="yaslink"></span>

### 4. plaintext\_password

该语句用于指定访问远端数据库的用户明文密码。

### 5. ciphertext\_password

该语句用于指定访问远端数据库的用户密文密码。

示例

```sql
-- YashanDB与YashanDB的数据库链接
ALTER PUBLIC DATABASE LINK dblink_yashan CONNECT TO REGRESS IDENTIFIED BY REGRESS;

-- YashanDB与Oracle的数据库链接
ALTER PUBLIC DATABASE LINK dblink_oracle CONNECT TO C##REGRESS IDENTIFIED BY REGRESS;
```