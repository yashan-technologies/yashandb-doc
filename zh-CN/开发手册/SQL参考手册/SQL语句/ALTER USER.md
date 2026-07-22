通用描述
----

ALTER USER用于修改用户属性，包括密码、默认表空间等，YashanDB的用户管理体系请参考[用户管理](../../../产品安全/身份标识与鉴别/管理用户)。

在容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）中，如需修改全局用户的属性建议连接根容器操作，其中更新密码以及使密码过期则必须连接根容器操作。

语句定义
----

**alter user::=**

```ebnf
= ALTER USER user_name 
(IDENTIFIED BY [VALUES] password
|DEFAULT TABLESPACE tablespace
|DEFAULT TABLESPACE SET tablespace_set
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK)
|PROFILE profilename
|quota_clause {" " quota_clause})
{" " 
(IDENTIFIED BY [VALUES] password
|DEFAULT TABLESPACE tablespace
|DEFAULT TABLESPACE SET tablespace_set
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK)
|PROFILE profilename
|quota_clause {" " quota_clause})
}
[CONTAINER "=" (CURRENT|ALL)].
```

### user\_name

已存在的用户名。

如果用户名中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。

### VALUES

该语句用于指定按密文修改用户的密码，若省略则按明文修改密码。

YashanDB的密码策略为加密传输和加密存储，即服务端存储的是密文密码。在某些特定场景（例如数据库迁移），可通过直接指定密文来修改密码，该操作不影响用户前端输入明文密码登录。

### password

为用户指定的新密码，新密码需遵循与[CREATE USER](./CREATE USER)中一致的password要求。

### tablespace

为用户指定默认表空间。

### tablespace_set

为用户指定默认表空间集，使用规则同[CREATE USER](./CREATE USER)。

### PASSWORD EXPIRE

使用户密码失效，用户密码失效后，用户无法登录，需要重新设置密码后方可登录。

### ACCOUNT (LOCK|UNLOCK)

锁定/解锁用户，锁定后该用户将不可登录。

对于容器数据库中的全局用户，若连接根容器进行锁定操作只能全局锁定，即连接根容器且指定CONTAINER = ALL。

<span id="profile" name="profile"></span>

### PROFILE profilename

为用户指定新的profile，通过dba_profiles视图可查询所有profile及其内容。如需新建profile请查阅[CREATE PROFILE](./CREATE PROFILE)。

### quota\_clause

使用规则同CREATE USER中的[quota_clause](./CREATE USER.md#quotaclause)。

### CONTAINER



该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），用于指定当前操作的容器作用域，可指定全局生效或当前容器本地生效，省略时默认为本地生效。

CONTAINER属性可选值如下：

- CURRENT：表示仅在当前所连接的容器本地生效，直连PDB时只能指定为该值。  

- ALL：表示全局生效，仅连接根容器时可用。



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
ALTER USER sales3 quota 100M on tablespace1;

ALTER USER c##sale1 PROFILE c##prof_all;
```
