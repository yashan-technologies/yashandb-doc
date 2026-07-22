DBMS_AWR_EXTRA包提供了一个内置的存储过程，用于辅助[DBMS_AWR](./DBMS_AWR.md)高级包功能实现。

> **Caution**:
>
> DBMS_AWR_EXTRA高级包只能通过[DBMS_AWR](./DBMS_AWR.md)高级包或[DBMS_WORKLOAD_REPOSITORY](./DBMS_WORKLOAD_REPOSITORY.md)高级包内部调用，不允许用户直接调用该高级包。

## UPDATE_SQLSTAT_LAST_SNAP

```plsql
DBMS_AWR_EXTRA.UPDATE_SQLSTAT_LAST_SNAP();
```

UPDATE_SQLSTAT_LAST_SNAP子程序为存储过程，无参数，用于创建快照时更新plan cache内每个SQL的统计项的last snap值为对应统计项的当前值。

## UPDATE_ASH_FLUSH_ID

```plsql
DBMS_AWR_EXTRA.UPDATE_ASH_FLUSH_ID(ash_flush_id IN NUMBER)
```

UPDATE_ASH_FLUSH_ID子程序用于刷盘后更新缓冲区的记录，即更新缓冲区中与当前已刷盘的最新样本ID、最新样本ID相对应的记录ID。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| ash_flush_id | 刷盘的最新样本ID      |

## ASH_FLUSH

```plsql
DBMS_AWR_EXTRA.ASH_FLUSH(snap_id IN NUMBER)
```

DBMS_AWR_EXTRA.ASH_FLUSH子程序用于创建快照后调用ASH记录持久化。

在共享集群/分布式集群部署中，内部调用该存储过程时会自行保证对所有实例的相应缓冲区按需进行刷盘。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| snap_id | 快照ID      |
