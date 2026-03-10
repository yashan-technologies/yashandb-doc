通用描述
----

DROP TRIGGER语句用于删除一个已存在的[触发器](../../全部手册/开发手册/PL参考手册/PL对象/触发器)。

语句定义
----

**DROP TRIGGER::=**

```ebnf+diagram
syntax::=  DROP TRIGGER [IF EXISTS] [ schema "." ]  trigger_name
```

### 1. IF EXISTS

该语句用于指定在DROP触发器之前，先判断该触发器是否存在。省略则不会判断，此时如果要删除的触发器不存在，系统将提示错误。

### 2. schema

包含触发器的模式名称，省略时默认为当前登录用户的模式。

### 3. trigger\_name

将要删除的触发器的名称。

示例（单机、共享集群部署）

```sql
-- 删除sales模式的tri触发器
DROP TRIGGER sales.tri;
```
