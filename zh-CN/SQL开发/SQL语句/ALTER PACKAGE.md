通用描述
----

ALTER PACKAGE语句用于显式的重编译一个自定义高级包的HEAD、BODY或整个自定义高级包。  

对于在SYS schema内的自定义高级包，需要由SYS用户执行ALTER PACKAGE语句。

对于其他schema内的自定义高级包，需要由其所属用户或拥有ALTER ANY PROCEDURE权限的用户执行ALTER PACKAGE语句。

自定义高级包含义及限制请参考[自定义高级包](../../全部手册/开发手册/PL参考手册/PL对象/自定义高级包)章节。

语句定义
----

**alter_package::=**

```ebnf+diagram
syntax::= ALTER PACKAGE [schema "."] package_name (package_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

包含自定义高级包的schema名称，省略则默认为当前登录用户的schema。

### 2. package\_name

需要显式重编译的自定义高级包名称。

### 3. package\_compile\_clause

指定重编译选项，详见[compile_clause](../../全部手册/开发手册/SQL参考手册/通用SQL语法/compile_clause)描述。

### 4. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

示例（单机、共享集群部署）

```sql
ALTER PACKAGE calc_fee COMPILE;

ALTER PACKAGE calc_fee COMPILE PACKAGE;

ALTER PACKAGE calc_fee COMPILE SPECIFICATION;

ALTER PACKAGE calc_fee COMPILE BODY;
```
