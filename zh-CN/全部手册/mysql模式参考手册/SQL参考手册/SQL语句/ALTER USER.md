## 通用描述

ALTER USER用于修改用户属性，包括密码、默认表空间等。YashanDB的用户管理体系请参考产品安全手册[用户管理](../../产品安全/管理用户)。

## 语句定义

**alter user::=**

```ebnf+diagram
syntax::= ALTER USER user_name 
(IDENTIFIED BY password
|PASSWORD EXPIRE
|ACCOUNT (LOCK|UNLOCK))
```

### user\_name

已存在的用户名。

如果用户名中包含特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，指定时需使用双引号包围，确保解析成功。

### IDENTIFIED BY password

为用户指定的新密码，新密码需遵循与[CREATE USER](./CREATE USER)中一致的password要求。

### PASSWORD EXPIRE

使用户密码失效，用户密码失效后，用户无法登录，需要重新设置密码后方可登录。

### ACCOUNT (LOCK|UNLOCK)

锁定/解锁用户，锁定后该用户将不可登录。

示例（HEAP表）

```sql
ALTER USER sales1 IDENTIFIED BY 'your%Password1';

ALTER USER sales2 PASSWORD EXPIRE;

ALTER USER sales3 ACCOUNT UNLOCK;

ALTER USER sales4 PASSWORD EXPIRE ACCOUNT LOCK;
```
