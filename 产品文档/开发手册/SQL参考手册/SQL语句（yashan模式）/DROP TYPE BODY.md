通用描述
----

DROP TYPE BODY语句用于删除一个已存在的类型主体。

语句定义
----

**DROP TYPE BODY::=**

```ebnf+diagram
syntax::=  DROP TYPE BODY [IF EXISTS] [ schema "." ]  type_name
```

### 1. IF EXISTS

该语句用于指定在删除类型主体之前，先判断该类型主体是否存在。省略则不会判断，此时如果要删除的类型主体不存在，系统将提示错误。


### 2. schema

包含类型主体的模式名称，省略时默认为当前登录用户的模式。

### 3. type\_name

将要类型主体的名称。

示例（单机、共享集群部署）

```sql
-- 删除当前模式的udt_object类型主体，不删除已存在的udt_object类型。
DROP TYPE BODY udt_object;
```

