通用描述
----

DROP TYPE语句用于删除一个已存在的[自定义类型](../../PL参考手册/PL对象/自定义类型)（UDT），同时会删除该类型存在的类型主体。

语句定义
----

**DROP TYPE::=**

```ebnf+diagram
syntax::=  DROP TYPE [IF EXISTS] [ schema "." ]  type_name [FORCE]
```

### 1. IF EXISTS

该语句用于指定在删除UDT之前，先判断该UDT是否存在。省略则不会判断，此时如果要删除的UDT不存在，系统将提示错误。

### 2. schema

包含UDT的模式名称，省略时默认为当前登录用户的模式。

### 3. type\_name

将要删除的UDT的名称。

### 4. FORCE

当类型被其它对象依赖时，FORCE选项可以强制删除该类型及类型主体。

示例（单机、共享集群部署）

```sql
-- 删除当前模式的udt_object自定义类型，会同时删除udt_object类型主体（如果存在）。
DROP TYPE udt_object;
```
