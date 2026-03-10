## 通用描述

CREATE VIEW用于创建一个视图。

不同SCHEMA下可以创建同名视图。

## 语句定义

**create view::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [ALGORITHM "=" {UNDEFINED | MERGE | TEMPTABLE}] [DEFINER "=" user] [SQL SECURITY { DEFINER | INVOKER}] VIEW [schema "."] view_name AS select_statement [WITH [CASCADED | LOCAL] CHECK OPTION]
```

### ALGORITHM

仅完成语法兼容，无实际含义。

### DEFINER

支持username和username@hostname两种语法形式。

- username支持CURRENT_USER和CURRENT_USER()关键字的语法解析且用户识别为当前会话用户；支持常规名称，常规名称遵循yashan命名规则且可以用反引号括起。

- hostname支持语法解析，包括%和常规名称，常规名称遵循yashan命名规则。

### SQL SECURITY

查询视图时，会查询视图中引用到的对象（表、视图等），该语句用于定义视图执行时使用的安全上下文：

- DEFINER，视图查询时查询用户只需要有访问视图的权限，但定义者需要拥有视图所引用对象的访问权限。

- INVOKER，视图查询时查询用户需要有访问视图的权限，也需要拥有视图所引用对象的访问权限。

不指定SQL SECURITY时默认为DEFINER。

### WITH CHECK OPTION

仅完成语法兼容，无实际含义。


示例（单机HEAP表）

```sql
mysql> create or replace algorithm = merge definer = user1 sql security definer view view_test1 as select * from example1.area with local check option;
Query OK, 0 rows affected (0.03 sec)
```