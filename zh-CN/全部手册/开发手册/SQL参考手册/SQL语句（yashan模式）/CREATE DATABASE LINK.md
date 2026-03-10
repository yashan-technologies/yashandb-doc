通用描述
----

CREATE DATABASE LINK语句用于创建一个数据库链接对象。本地当前用户通过数据库链接可以访问远程数据库中某用户的物理表数据。

创建公有数据库链接的用户须拥有CREATE PUBLIC DATABASE LINK系统权限，创建非公有数据库链接用户须拥有CREATE DATABASE LINK系统权限。

根据数据库系统的结构，支持两种数据库链接：

- 同构数据库链接：YashanDB与YashanDB的数据库链接
- 异构数据库链接：YashanDB与Oracle的数据库链接，需要进行[异构数据库链接配置](../../../数据库管理/基本数据库管理/异构数据库链接配置)

语句定义
----

**[create database link](#createdatabaselink)::=**

```ebnf+diagram
syntax::= CREATE [PUBLIC] DATABASE LINK dblink_name
[connect_clause] [USING connect_string]
```

**[connect\_clause](#connectclause)::=**

```ebnf+diagram
syntax::= CONNECT TO username IDENTIFIED BY pwd_clause
```

**[pwd\_clause](#pwdclause)::=**

```ebnf+diagram
syntax::= plaintext_password | VALUES ['"'] ciphertext_password ['"']
```

**[connect\_string](#connectstring)::=**

```ebnf+diagram
syntax::= [database_type ":"] url ["/" db_name]
```

**[database\_type](#databasetype)::=**

```ebnf+diagram
syntax::= [YASHAN | ORACLE]
```

<span id="createdatabaselink" name="createdatabaselink" class="yaslink"></span>

### 1. PUBLIC

该语句建立一个公有数据库链接，对所有数据库用户可见。

### 2. dblink\_name

该语句用于指定创建的数据库链接的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="connectclause" name="connectclause" class="yaslink"></span>

### 3. username

该语句用于指定访问远端数据库的用户名。

<span id="pwdclause" name="pwdclause" class="yaslink"></span>

### 4. plaintext\_password

该语句用于指定访问远端数据库的用户明文密码。

### 5. VALUES ciphertext\_password

该语句用于指定访问远端数据库的用户密文密码。

<span id="connectstring" name="connectstring" class="yaslink"></span>

### 6. connect\_string

该语句用于指定连接远程数据库的连接串信息。

创建DATABASE LINK时不会校验连接串的正确性（使用时才会进行校验）且ALTER DATABASE LINK语句无法修改连接串，请确保指定为正确的配置，否则只能删除后重新创建。

<span id="databasetype" name="databasetype" class="yaslink"></span>

#### 6.1. database\_type

该语句用于指定远程数据库系统或标准接口标签名，支持识别Yashan和Oracle，省略时默认为Yashan。

#### 6.2. url

该语句用于指定远程数据库的连接地址，例如192.168.1.2:1688。

#### 6.3. db\_name

该语句用于指定远程Oracle数据库的名称。

若远程数据库为YashanDB，不能指定数据库名称。

示例

```sql
-- YashanDB与YashanDB的数据库链接
CREATE DATABASE LINK dblink_yashan CONNECT TO sales IDENTIFIED BY sales USING '192.168.1.2:1688';

-- YashanDB与Oracle的数据库链接，且对所有用户可见
CREATE PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS IDENTIFIED BY REGRESS USING 'oracle:192.168.1.2:1521/orcl';
```
