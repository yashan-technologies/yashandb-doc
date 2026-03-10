DBMS_PART包提供了一组清理全局索引孤儿键值对的存储过程/函数。

## CLEANUP\_GIDX\_JOB

```plsql
DBMS_PART.CLEANUP_GIDX_JOB (
   options   IN  VARCHAR DEFAULT 'CLEANUP_ORPHANS'
);
```

CLEANUP_GIDX_JOB函数用于清理库中所有包含孤儿键值对的全局索引，以提高索引性能并减少空间使用。

无法执行coalesce cleanup的索引类型，例如RTree索引，则会跳过。

|  参数| 描述|
| :---- |:-----------|
|options| 清理选项。CLEANUP_ORPHANS：表示使用coalesce cleanup only。COALESCE：表示使用coalesce cleanup。详见[COALESCE](../SQL语句/ALTER INDEX.html#coalesce) |

示例

```plsql

exec DBMS_PART.CLEANUP_GIDX_JOB('CLEANUP_ORPHANS');
```
