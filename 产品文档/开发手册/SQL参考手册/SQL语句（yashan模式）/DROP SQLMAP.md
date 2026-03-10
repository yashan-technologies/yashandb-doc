通用描述
----

DROP SQLMAP用于删除一个SQL映射。

用户必须拥有DBA权限才能删除一个SQL映射。

在分布式部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**drop sqlmap::=**

```ebnf+diagram
syntax::= DROP SQLMAP sqlmap_name
```

### 1. sqlmap\_name

该语句用于指定要删除的SQL映射的名称。



示例

```sql
DROP SQLMAP map_branch;
```

