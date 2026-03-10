通用描述
----

ALTER USER用于修改用户属性，包括密码、默认表空间等。YashanDB的用户管理体系请参考产品安全手册[用户管理](../../全部手册/产品安全/身份标识与鉴别/管理用户)。

语句定义
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
```

### 1. user\_name

已存在的用户名。

如果用户名中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。

### 2. VALUES

该语句用于指定按密文修改用户的密码，若省略则按明文修改密码。

YashanDB的密码策略为加密传输和加密存储，即服务端存储的是密文密码。在某些特定场景（例如数据库迁移），可通过直接指定密文来修改密码，该操作不影响用户前端输入明文密码登录。

### 3. password

为用户指定的新密码，新密码需遵循与[CREATE USER](./CREATE USER)中一致的password要求。

### 4. tablespace

为用户指定默认表空间。

### 5. tablespace\_set

为用户指定默认表空间集，使用规则同[CREATE USER](./CREATE USER)。

### 6. PASSWORD EXPIRE

使用户密码失效，用户密码失效后，用户无法登录，需要重新设置密码后方可登录。

### 7. ACCOUNT (LOCK|UNLOCK)

锁定/解锁用户，锁定后该用户将不可登录。

<span id="profile" name="profile" class="yaslink"></span>

### 8. PROFILE profilename

为用户指定新的profile，通过DBA_PROFILES视图可查询所有profile及其内容。如需新建profile请查阅[CREATE PROFILE](./CREATE PROFILE)。

### 9. quota\_clause

使用规则同CREATE USER中的[quota_clause](./CREATE USER.html#quotaclause)。

示例

```sql
ALTER USER sales1 IDENTIFIED BY "23%ad1";
  
ALTER USER sales2 DEFAULT TABLESPACE users;

ALTER USER sales3 PASSWORD EXPIRE;

ALTER USER sales3 ACCOUNT UNLOCK;

ALTER USER sales3 PASSWORD EXPIRE ACCOUNT LOCK;

ALTER USER sales3 PROFILE DEFAULT;
```

示例（单机/共享集群/分布式集群部署）

```sql
ALTER USER sales3 quota 100M ON tablespace1;
```
