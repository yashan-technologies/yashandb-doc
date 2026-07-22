## 通用描述

DROP LIBRARY用于删除一个已有的[自定义库](../../PL参考手册/PL对象/自定义库)。

普通用户只能删除自己创建的自定义库。删除其他用户的自定义库，需要有DROP ANY LIBRARY权限。

当要删除的自定义库被外置UDF引用时，删除本自定义库会导致该外置UDF运行时提示错误。

## 语句定义

**drop library::=**

```ebnf
= DROP LIBRARY [IF EXISTS] [schema "."] library_name.
```

### IF EXISTS

该语句用于指定在DROP自定义库之前，先判断该自定义库是否存在，省略则不会判断，此时如果要删除的自定义库不存在，系统将提示错误。

示例（单机/共享集群/分布式集群部署）

```sql
DROP LIBRARY IF EXISTS ya_lib;
```
