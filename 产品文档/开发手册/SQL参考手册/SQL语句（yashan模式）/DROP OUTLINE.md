通用描述
----

DROP OUTLINE用于删除一个存储纲要。

用户必须拥有DROP ANY OUTLINE权限才能删除一个存储纲要。

在分布式部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**drop\_outline::=**

```ebnf+diagram
syntax::= DROP [(PUBLIC)] OUTLINE outline_name
```

### 1. PUBLIC
公有模式，默认值。

### 2. outline\_name

将要删除的OUTLINE的名称。

示例

```sql
-- 删除指定的OUTLINE
DROP OUTLINE ol_a;
```
