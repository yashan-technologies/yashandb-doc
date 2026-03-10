通用描述
----

PURGE语句用于清理回收站数据。

YashanDB中，某个表被DROP或TRUNCATE时，如果开启了回收站功能（[修改配置参数](./ALTER SYSTEM)RECYCLEBIN_ENABLED为ON），这个表的数据将被放入回收站，用于误操作的恢复。

PURGE仅适用于HEAP表。

语句定义
----

**purge::=**

```ebnf+diagram
syntax::= PURGE (TABLE name|INDEX name|TABLESPACE name [USER username]|RECYCLEBIN|DBA_RECYCLEBIN)
```

### 1. TABLE

该语句用于清理回收站中指定的表，清理表的同时会清理表上的其他对象，例如索引，LOB等。

name用于指定待清理的表名称，可以为：

- 原表名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的ORIGINAL_NAME字段）

- 目标表进入回收站时系统生成的BIN开头对象名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的OBJECT_NAME字段）

### 2. INDEX

该语句用于清理回收站中指定的索引。

name用于指定待清理的索引名，可以为：

- 原索引名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的ORIGINAL_NAME字段）

- 目标索引进入回收站时系统生成的BIN开头对象名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的OBJECT_NAME字段）

### 3. TABLESPACE name [USER username]

该语句用于清理回收站中指定的表空间，指定user时将只清理指定表空间下指定用户下的对象。

### 4. RECYCLEBIN

该语句用于清理回收站中当前用户下的所有对象。

### 5. DBA\_RECYCLEBIN

该语句用于清理回收站中的全部对象。

示例（HEAP表）

```sql
--开启回收站
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

--删除某个对象
DROP TABLE finance_info;
--清理回收站中指定的对象数据
PURGE TABLE finance_info;

--清理指定表空间的回收站数据
PURGE TABLESPACE yashan;
--清理回收站中当前用户下的所有对象数据
PURGE RECYCLEBIN;
--清理回收站中的全部对象数据
PURGE DBA_RECYCLEBIN;
```
