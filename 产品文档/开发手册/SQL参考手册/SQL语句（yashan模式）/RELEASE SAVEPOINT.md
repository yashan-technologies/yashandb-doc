通用描述
----

RELEASE SAVEPOINT用于从当前事务的一组保存点中删除某个保存点，删除后将无法再回退到该保存点。

语句定义
----

**release savepoint::=**

```ebnf+diagram
syntax::= RELEASE SAVEPOINT savepoint_name
```

示例

```sql
-- 设置保存点
SAVEPOINT sp_1;

-- 删除保存点
RELEASE SAVEPOINT sp_1;
```

