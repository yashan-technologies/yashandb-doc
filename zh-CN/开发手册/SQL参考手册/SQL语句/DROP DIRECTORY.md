通用描述
----

DROP DIRECTORY用于删除数据目录对象，执行本语句的用户需具有drop any directory权限。

语句定义
----

**drop directory::=**

```ebnf
= DROP DIRECTORY directory_name.
```

### directory_name

该语句用于指定待删除的目录名称，不可省略。

示例

```sql
DROP DIRECTORY dir;
```
