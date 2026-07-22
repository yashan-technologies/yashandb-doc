## 通用描述

CREATE USER用于新建一个数据库用户。系统默认创建的是普通用户，YashanDB的用户管理体系请参考产品安全手册[用户管理](../../产品安全/管理用户)。

## 语句定义

**create user::=**

```ebnf
= CREATE USER user_name 
(IDENTIFIED BY password
| ACCOUNT (LOCK|UNLOCK)
| PASSWORD EXPIRE).
```

### user\_name

该语句用于指定创建的用户的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)，但英文字母区分大小写。

如果用户名中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。后续执行ALTER USER、GRANT、REVOKE等需要指定用户名的操作时，也需采用双引号包围用户名。

因yashan模式和mysql模式大小写敏感区别，mysql模式创建的用户通过yasql工具连接时，连接串中的用户名需要使用双引号包围，通过MySQL Client连接时不需要使用双引号包围。

### IDENTIFIED BY password

该语句用于指定创建的用户的密码，可省略，省略则默认无密码。

该语句需满足如下规则：

*   不可指定为NULL或''。
*   可以为数字、字母和特殊符号的组合。
*   密码字符不可以为双引号。
*   当密码中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。
*   区分大小写。
*   密码字符串长度不超过64。
*   遵循[密码强度](../../产品安全/密码策略.md)要求。

> **Note**: 
>
> 无密码的用户无法连接至YashanDB中，创建新用户时建议执行本语句为该用户指定密码。 

**@和/的特殊处理**

YashanDB允许在密码中包含`@`和`/`等特殊符号，在输入密码进行登录时，包含`@`或`/`的密码字符串前后也必须使用双引号标识。

在Linux OS命令行模式中，双引号为特殊含义字符，对其的使用需用`\`进行转义或由单引号包裹。

示例（单机HEAP表）

```sql
-- 创建sales1和sales2两个用户，其中sales2用户密码包含了@和/
CREATE USER sales1 IDENTIFIED BY 'your_Password1';
CREATE USER sales2 IDENTIFIED BY "your@Password1";

-- 在SQL客户端连接时应输入的密码格式
SQL> conn "sales1"/your_Password1
SQL> conn "sales2"/"your@Password1"

-- 在yasql命令行工具连接时应输入的密码格式
$ yasql \"sales1\"/your_Password1
$ yasql \"sales2\"/\"your@Password1\"

-- 通过MySQL Client连接时应输入的密码格式
$ mysql -h ipaddr -P 1690 -u sales1 -p
your_Password1
```

### ACCOUNT (LOCK|UNLOCK)

该语句用于指定在创建用户时是否锁定该用户，若锁定则该用户将不可登录。

本语句可省略，若省略则默认为不锁定。

示例（单机HEAP表）

```sql
CREATE USER sales3 IDENTIFIED BY 'your_Password3' ACCOUNT LOCK;
```

### PASSWORD EXPIRE

该语句用于在创建用户时直接失效其初始密码，该用户在首次登录数据库时必须先修改密码。

示例（单机HEAP表）

```sql
CREATE USER sales4 IDENTIFIED BY 'your_Password4' PASSWORD EXPIRE; 
```
