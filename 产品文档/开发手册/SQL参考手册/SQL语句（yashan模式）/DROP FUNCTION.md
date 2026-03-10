通用描述
----

DROP FUNCTION用于删除一个已有的[自定义函数](../../PL参考手册/PL对象/自定义函数)。

普通用户只能删除自己创建的自定义函数。

当要删除的函数被其他过程体引用时，删除本函数会导致该过程体运行时报编译错误。

语句定义
----

**drop function::=**

```ebnf+diagram
syntax::= DROP FUNCTION [IF EXISTS] [schema "."] function_name
```

### 1. IF EXISTS

该语句用于指定在DROP 自定义函数之前，先判断该自定义函数是否存在，省略则不会判断，此时如果要删除的自定义函数不存在，系统将提示错误。

示例（单机、共享集群部署）

```sql
DROP FUNCTION IF EXISTS ya_func; 
```

