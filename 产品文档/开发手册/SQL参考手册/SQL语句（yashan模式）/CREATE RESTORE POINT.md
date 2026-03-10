通用描述
----

CREATE RESTORE POINT用于创建一个还原点，还原点可作为全库闪回的目标点。执行该语句需要用户拥有FLASHBACK ANY TABLE及以上的权限。

创建还原点前，需确保数据库已开启[全库闪回](ALTER DATABASE.html#flashbackdatabaseclauses)功能，且当前处于MOUNT或OPEN状态。

语句定义
----

**create restore point::=**

```ebnf+diagram
syntax::= CREATE RESTORE POINT restore_point_name [AS OF (SCN scn|TIMESTAMP timestamp)] [GUARANTEE FLASHBACK DATABASE]
```

### 1. restore\_point\_name

该语句用于指定要创建的还原点的名称，不可省略，名称需全局唯一且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 2. AS OF SCN|TIME

该语句用于指定要创建的还原点所对应的时间线，不指定则默认执行该语句当前的时间线。

指定的时间线不能早于V$FLASHBACK_DATABASE_LOG视图里显示的最老时间线。

### 3. GUARANTEE FLASHBACK DATABASE

指定该语句表示创建的还原点为永久还原点，省略则默认为普通（非永久）还原点。只有SYS用户才可以创建永久还原点。

数据库运行过程中或执行全库闪回操作时会涉及到全库闪回的资源维护，维护操作会清理所有普通还原点，若指定为永久还原点则不会被自动清理。

当存在永久还原点时，数据库将无法关闭全库闪回功能，需先手动[删除](DROP RESTORE POINT)所有永久还原点。

示例（单机部署）

```sql
-- 开启数据库闪回
ALTER DATABASE FLASHBACK ON;

-- 创建永久还原点并指定scn
CREATE RESTORE POINT p20241201 AS OF SCN 673515539901313024 GUARANTEE FLASHBACK DATABASE;

-- 创建普通还原点并指定scn
CREATE RESTORE POINT p202412011000 AS OF SCN 673515539901313024;

-- 创建普通还原点，不指定scn
CREATE RESTORE POINT p202412011100;
```
