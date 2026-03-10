通用描述
----

ALTER PROCEDURE语句用于显式的重编译一个存储过程。

对于在SYS schema内的存储过程，需要由SYS用户执行ALTER PROCEDURE语句。

对于其他schema内的存储过程，需要由其所属用户或拥有ALTER ANY PROCEDURE权限的用户执行ALTER PROCEDURE语句。

存储过程含义及限制请参考[存储过程](../../PL参考手册/PL对象/存储过程)章节。

语句定义
----

**alter procedure::=**

```ebnf+diagram
syntax::= ALTER PROCEDURE [schema "."] procedure_name (procedure_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

包含存储过程的模式名称，省略则默认为当前登录用户的模式。

### 2. procedure\_name

需要显式重编译的存储过程名称。

### 3. procedure\_compile\_clause

指定重编译选项，详见[compile_clause](../通用SQL语法/compile_clause)描述。

### 4. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

示例（单机、共享集群部署）

```sql
ALTER PROCEDURE sales.ya_proc COMPILE;
```
