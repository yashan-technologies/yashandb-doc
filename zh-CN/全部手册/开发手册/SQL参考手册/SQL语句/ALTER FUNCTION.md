通用描述
----

ALTER FUNCTION语句用于显式的重编译一个自定义函数。

对于在SYS schema内的自定义函数，需要由SYS用户执行ALTER FUNCTION语句。

对于其他schema内的自定义函数，需要由其所属用户或拥有ALTER ANY PROCEDURE权限的用户执行ALTER FUNCTION语句。

自定义函数含义及限制请参考[自定义函数](../../PL参考手册/PL对象/自定义函数)章节。

语句定义
----

**alter_function::=**

```ebnf+diagram
syntax::= ALTER FUNCTION [schema "."] function_name (function_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

包含自定义函数的模式名称，省略则默认为当前登录用户的模式。

### 2. function\_name

需要显式重编译的自定义函数名称。

### 3. function\_compile\_clause

指定重编译选项，详见[compile_clause](../通用SQL语法/compile_clause)描述。

### 4. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER FUNCTION sales.ya_func COMPILE;
```
