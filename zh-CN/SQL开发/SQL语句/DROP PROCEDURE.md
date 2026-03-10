通用描述
----

DROP PROCEDURE用于删除一个已有的[存储过程](../../全部手册/开发手册/PL参考手册/PL对象/存储过程)。

普通用户只能删除自己创建的存储过程。

当要删除的存储过程被其他过程体引用时，删除本存储过程会导致该过程体运行时报编译错误。

语句定义
----

**drop procedure::=**

```ebnf+diagram
syntax::= DROP PROCEDURE [IF EXISTS] [schema "."] procedure_name
```

### 1. IF EXISTS

该语句用于指定在DROP 存储过程之前，先判断该存储过程是否存在，省略则不会判断，此时如果要删除的存储过程不存在，系统将提示错误。

示例（单机/共享集群/分布式集群部署）

```plsql
DROP PROCEDURE IF EXISTS ya_proc;
```
