通用描述
----

DROP VIEW用于删除一个视图，包括处于失效状态中的视图。

语句定义
----

**drop view::=**

```ebnf+diagram
syntax::= DROP VIEW [IF EXISTS] [ schema "." ] view_name
```

### 1. IF EXISTS

该语句用于指定在DROP视图之前，先判断该视图是否存在，若视图不存在则返回成功。省略则不会判断，此时如果要删除的视图不存在，系统将提示错误。

### 2. schema

目标视图所属的模式名称，省略时默认为当前登录用户的模式。

### 3. view\_name

该语句用于指定要删除的视图的名称。

示例

```sql
DROP VIEW IF EXISTS v_area;
```

