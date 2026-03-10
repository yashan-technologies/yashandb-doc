UNDO表空间用于YashanDB创建和管理回滚（撤销数据库更改）信息，这种信息包括交易行为的记录，且主要是在交易提交之前，统称为undo。

undo记录用于：

-   执行ROLLBACK语句回滚事务

-   恢复数据库

-   提供读取一致性

-   使用闪回查询分析较早时间点的数据

-   使用闪回功能从逻辑损坏中恢复

**设置最短撤销保留期**

YashanDB提供一种自动化的机制，称为自动撤销管理，用于管理undo信息和空间。

当启用自动撤销管理时，始终存在一个当前撤销保持期，这是YashanDB在覆盖旧的撤销信息之前尝试保留该信息的最短时间。

通过UNDO_RETENTION参数指定最小撤销保持期（以秒为单位）：

```sql
ALTER SYSTEM SET UNDO_RETENTION = 2400;
```
**为UNDO表空间添加数据文件**

> **Note**：
>
> 指定数据文件路径时，单机部署中可按绝对或相对路径指定，共享集群部署中按绝对路径指定且只能为YFS路径，分布式部署中只允许按相对路径指定。

示例（单机、分布式部署）

```sql
ALTER TABLESPACE UNDO ADD DATAFILE '?/dbfiles/undo02' SIZE 10M AUTOEXTEND ON;
```

示例（共享集群部署）

```sql
ALTER TABLESPACE undo0 ADD DATAFILE '+DG0/undo02' SIZE 10M AUTOEXTEND ON;
```

**为UNDO表空间调整数据文件大小**

此功能仅适用于单机部署，且UNDO表空间的数据文件只能扩大，不能缩小。

示例（单机部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/undo02' RESIZE 20M;
```

**删除UNDO表空间**

分布式部署中无法使用此功能。

示例（单机、共享集群部署）

```sql
DROP TABLESPACE undo0 INCLUDING CONTENTS AND DATAFILES;
```

>  **Note**：
>
>  只有当前未被任何实例使用时，才能删除UNDO表空间。

**查看UNDO相关统计信息** 

示例

```sql
SELECT ID,BLK_REUSE,BALANCE_TIME,BALANCE,BALANCE_BLK,RECYCLE_TIME,RECYCLE_LIST,RECYCLE_LIST_BLK FROM V$UNDOSTAT;

  ID    BLK_REUSE BALANCE_TIME          BALANCE  BALANCE_BLK RECYCLE_TIME      RECYCLE_LIST RECYCLE_LIST_BLK
---- ------------ ---------------- ------------ ------------ ----------------- ------------ ----------------
   0            9 2022-07-04                425          112 1970-01-01                   0                0
   1            6 2022-07-04                425          216 1970-01-01                   0                0
   2            8 2022-07-04                425          326 1970-01-01                   0                0
   3            9 2022-07-04                425          437 1970-01-01                   0                0
   4            9 2022-07-04                425          564 1970-01-01                   0                0
```

**查看UNDO SEGMENT信息**

示例

```sql
SELECT ID,USED_TIME,FIRST_UBAFIL,FIRST_UBABLK,LAST_UBAFIL,LAST_UBABLK,UFB_COUNT,FIRST_UFBFIL,FIRST_UFBBLK FROM V$UNDO_SEGMENTS;

 ID USED_TIME      FIRST_UBAFIL FIRST_UBABLK  LAST_UBAFIL  LAST_UBABLK    UFB_COUNT FIRST_UFBFIL FIRST_UFBBLK
--- -------------- ------------ ------------ ------------ ------------ ------------ ------------ ------------
  0 2022-07-04                0         7012            0         7012           15            0         7013
  1 2022-07-04                0         6472            0         6472            7            0         6473
  2 2022-07-04                0         6510            0         6510            1            0         6511
  3 2022-07-04                0         6883            0         6883            0            0         6885
  4 2022-07-04                0         6527            0         6527            0            0         6528
```

