DBMS_AWR_EXTRA包提供了一个内置的存储过程，用于辅助[DBMS_AWR](./DBMS_AWR)高级包功能实现。

> **Caution**:
>
> - DBMS_AWR_EXTRA高级包只能通过[DBMS_AWR](./DBMS_AWR)高级包的CREATE_SNAPSHOT()子存储过程调用，不允许用户直接调用该高级包。
>
> - DBMS_AWR_EXTRA高级包的其他使用约束同DBMS_AWR高级包。

## UPDATE\_SQLSTAT\_LAST\_SNAP

```plsql
DBMS_AWR_EXTRA.UPDATE_SQLSTAT_LAST_SNAP();
```

UPDATE_SQLSTAT_LAST_SNAP子程序为存储过程，无参数，用于创建快照时更新plan cache内每个SQL的统计项的last snap值为对应统计项的当前值。