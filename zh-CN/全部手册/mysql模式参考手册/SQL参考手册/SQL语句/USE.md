## 通用描述

USE用于指定当前会话中后续操作的目标数据库，在会话结束或再次执行USE语句指向其他数据库之前都默认操作该数据库。

指定数据库后，如需访问其他数据库中的对象（表或视图等），其名称需指定为`database_name.object_name`格式。

## 语句定义

**use::=**

```ebnf+diagram
syntax::= USE database_name
```

示例（单机HEAP表）

```sql
USE sales;
```
