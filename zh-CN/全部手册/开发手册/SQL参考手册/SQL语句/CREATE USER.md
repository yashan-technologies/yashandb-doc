通用描述
----

CREATE USER用于新建一个数据库用户。系统默认创建的是普通用户，YashanDB的用户管理体系请参考产品安全手册[用户管理](../../../产品安全/身份标识与鉴别/管理用户)。

语句定义
----

**create user::=**

```ebnf+diagram
syntax::= CREATE USER user_name 
(IDENTIFIED BY [VALUES] password
| DEFAULT TABLESPACE tablespace
| DEFAULT TABLESPACE SET tablespace_set
| TEMPORARY TABLESPACE tablespace
| LOCAL TEMPORARY TABLESPACE tablespace
| PROFILE profilename
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE
| (quota_clause) {" " (quota_clause)})
{" " 
(IDENTIFIED BY [VALUES] password
| DEFAULT TABLESPACE tablespace
| DEFAULT TABLESPACE SET tablespace_set
| TEMPORARY TABLESPACE tablespace
| LOCAL TEMPORARY TABLESPACE tablespace
| PROFILE profilename
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE
| (quota_clause) {" " (quota_clause)})}
```

**[quota\_clause](#quotaclause)::=**

```ebnf+diagram
syntax::= 
((QUOTA | FLOOR) (size_clause | UNLIMITED) ON tablespace )
{" " ((QUOTA | FLOOR) (size_clause | UNLIMITED) ON tablespace )}
```

### 1. user\_name

该语句用于指定创建的用户的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

如果用户名中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。后续执行ALTER USER、GRANT、REVOKE等需要指定用户名的操作时，也需采用双引号包围用户名。

如果使用双引号包围，则字母区分大小写。

### 2. VALUES

该语句用于指定按密文创建用户的密码，可省略，则按明文创建密码。

YashanDB的密码策略为加密传输和加密存储，即服务端存储的是密文密码。在某些特定场景（例如数据库迁移），可通过直接指定密文来创建密码，该操作不影响用户前端输入明文密码登录。

### 3. IDENTIFIED BY password

该语句用于指定创建的用户的密码，可省略，省略则默认无密码。

该语句需满足如下规则：

*   不可指定为NULL或''。
*   可以为数字、字母和特殊符号的组合。
*   密码字符不可以为双引号。
*   当密码中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。
*   区分大小写。
*   密码字符串长度不超过64。
*   遵循[密码强度](../../../产品安全/身份标识与鉴别/密码认证/密码策略.html#password_complexity)和[密码策略](../../../产品安全/身份标识与鉴别/密码认证/密码策略.html#password_policy)要求。

> **Note**: 
>
> 无密码的用户无法连接至YashanDB中，创建新用户时建议执行本语句为该用户指定密码。 

当指定VALUES时，password内容为密文，不遵循上述规则，但需符合如下基本格式要求，否则创建用户失败且提示YAS-04289错误：

- 必须以`S:`开头。
- 整个长度必须是86字节。
- `S:`后只能是数字和大写的字母。

**@和/的特殊处理**

YashanDB允许在密码中包含`@`和`/`等特殊符号，在输入密码进行登录时，包含`@`或`/`的密码字符串前后也必须使用双引号标识。

在Linux OS命令行模式中，双引号为特殊含义字符，对其的使用需用`\`进行转义或由单引号包裹。

示例

```sql
-- 创建sales1和sales2两个用户，其中sales2用户密码包含了@和/
CREATE USER sales1 IDENTIFIED BY "1%2";
CREATE USER sales2 IDENTIFIED BY "1@2/";

-- 需至少授予用户CREATE SESSION权限，以使用户能连接数据库
GRANT CREATE SESSION TO sales1;
GRANT CREATE SESSION TO sales2;

-- 在SQL客户端连接时应输入的密码格式
conn sales1/1%2
conn sales2/"1@2/"

-- 在命令行连接时应输入的密码格式
$ yasql sales1/1%2
$ yasql sales2/\"1@2/\"
```

### 4. DEFAULT TABLESPACE

该语句用于指定创建的用户的默认表空间，可省略，则缺省为数据库创建时的DEFAULT表空间。

示例

```sql
CREATE USER sales3 IDENTIFIED BY 123 DEFAULT TABLESPACE users;
```

### 5. DEFAULT TABLESPACE SET

该语句用于指定用户的默认表空间集，要求对应的表空间集已存在。可省略，省略则缺省为 USERS 表空间集。

该语句仅适用于存算一体分布式集群部署。

示例（存算一体分布式集群部署）

```sql
CREATE TABLESPACE SET sales_tss ON USERS MAXSIZE 1G;
CREATE USER sales123 IDENTIFIED BY 123 DEFAULT TABLESPACE SET sales_tss;
```

### 6. TEMPORARY TABLESPACE

该语句用于指定创建的用户的默认TEMP表空间，可省略，则缺省为数据库创建时的TEMP表空间。

示例

```sql
CREATE USER sales3 IDENTIFIED BY 123 TEMPORARY TABLESPACE temp;
```

### 7. LOCAL TEMPORARY TABLESPACE

该语句用于指定创建的用户的默认本地TEMP表空间，可省略，缺省值为空，此时用户创建临时表默认使用TEMP表空间。

如同时指定本地TEMP表空间与TEMP表空间，优先使用本地TEMP表空间。

存算一体分布式集群部署/单机部署中无法使用此功能。

示例（共享集群部署）

```sql
CREATE USER sales3 IDENTIFIED BY 123 LOCAL TEMPORARY TABLESPACE local_temp;
```

<span id="profile" name="profile" class="yaslink"></span>

### 8. PROFILE profilename

该语句用于为创建的用户指定对应的profile，通过DBA_PROFILES视图可查询所有profile及其内容。如需新建profile请查阅[CREATE PROFILE](./CREATE PROFILE)。

本语句可省略，则缺省对应系统默认的profile。

示例

```sql
CREATE USER sales5 IDENTIFIED BY 123 PROFILE DEFAULT;
```

### 9. ACCOUNT (LOCK|UNLOCK)

该语句用于指定在创建用户时是否锁定该用户，若锁定则该用户将不可登录。

本语句可省略，若省略则默认为不锁定。

示例

```sql
CREATE USER sale4 IDENTIFIED BY 123 ACCOUNT LOCK;
```

### 10. PASSWORD EXPIRE

该语句用于在创建用户时直接失效其初始密码，该用户在首次登录数据库时必须先修改密码。

示例

```sql
CREATE USER sale8 IDENTIFIED BY 123 PASSWORD EXPIRE;
```

<span id="quotaclause" name="quotaclause" class="yaslink"></span>

### 11. quota\_clause

该语句用于为用户指定表空间配额，可省略，该语句不适用于存算一体分布式集群部署。

表空间配额包括上限与下限，上限指用户可以在对应表空间使用的最大空间，下限指表空间需要为用户预留的最少空间。

不指定表空间配额或上下限均指定为UNLIMITED的情况下，用户默认可以任意使用表空间。

表空间配额的配置规则如下：

- 当指定的上限值或下限值不等于单个BLOCK大小的整数倍时，会自动向上取整为BLOCK的整数倍的值。
- 下限值不得大于上限值。
- 如需为用户在某个表空间上配置表空间配额的下限约束，需确保该表空间的预留空间充足，否则会报错。
- 不能对SYSTEM表空间配置表空间配额的下限约束。
- sys用户、拥有DBA角色的用户均不受表空间配额的上限约束，且不能为sys用户配置表空间配额约束（即使语句执行成功也不会生效）。

#### 11.1. QUOTA(size\_clause|UNLIMITED)

QUOTA关键字用于为用户指定表空间配额的上限，可省略。

省略或指定为UNLIMITED表示无空间使用上限约束。

上限值需为正数且不能超过2T，单位为字节，其数值可以为阿拉伯数字（例如1、20或100等）或含量级单位的数值（例如2K、3M、4G或1T等）。

#### 11.2. FLOOR(size\_clause|UNLIMITED)

FLOOR关键字用于为用户指定表空间配额的下限，可省略。

省略或指定为UNLIMITED表示不预留空间。

下限值需为正数且不能超过2T，单位为字节，其数值可以为阿拉伯数字（例如1、20或100等）或含量级单位的数值（例如2K、3M、4G或1T等）。

示例（单机、共享集群部署）

```sql
CREATE USER sale5 IDENTIFIED BY u1 QUOTA 100M FLOOR 10000 ON tablespace1;

CREATE USER sale6 IDENTIFIED BY u2 QUOTA UNLIMITED ON tablespace2;

CREATE USER sale7 IDENTIFIED BY u3 FLOOR 20M ON tablespace3 QUOTA 1G ON tablespace4;
```
