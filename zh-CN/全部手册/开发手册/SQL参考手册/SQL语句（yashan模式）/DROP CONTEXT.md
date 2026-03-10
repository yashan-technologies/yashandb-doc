通用描述
----

DROP CONTEXT用于删除上下文对象，执行本语句的用户需具有drop any context权限。

语句定义
----

**drop context::=**

```ebnf+diagram
syntax::= DROP CONTEXT namespace
```

### 1. namespace

该语句用于指定待删除的上下文名称，不可省略。

示例 （单机、共享集群部署）

```sql
DROP CONTEXT ctx1;
```
