通用描述
----

DROP ROLE用于删除一个存在的角色。角色被删除时，系统自动对已授权该角色的用户和角色进行角色收回。

系统内置角色不允许删除。

在容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）中，如需删除全局角色，必须连接根容器操作。

语句定义
----

**drop user::=**

```ebnf+diagram
syntax::= DROP ROLE role
```

### 1. role

该语句用于指定要删除的角色的名称。

示例

```sql
DROP ROLE rolename;
```
