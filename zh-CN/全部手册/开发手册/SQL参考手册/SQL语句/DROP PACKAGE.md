通用描述
----

DROP PACKAGE用于删除一个已有的[自定义高级包](../../PL参考手册/PL对象/自定义高级包)。

普通用户只能删除自己创建的高级包。

当要删除的高级包的变量或子对象被其他过程体引用时，删除本高级包会导致该过程体运行时报编译错误。

语句定义
----

**drop package::=**

```ebnf+diagram
syntax::= DROP PACKAGE [BODY] [IF EXISTS] [schema "."] package_name
```

### 1. BODY

用于指定是否只删除自定义高级包的BODY。当不指定BODY时，将同时删除PACKAGE HEAD和PACKAGE BODY。

### 2. IF EXISTS

该语句用于指定在删除自定义高级包之前，先判断该自定义高级包是否存在，省略则不会判断，此时如果要删除的自定义高级包不存在，系统将提示错误。

示例（单机/共享集群/分布式集群部署）

```sql
DROP PACKAGE IF EXISTS calc_fee;
```
