The DBMS_AWR_EXTRA package provides a built-in stored procedure to assist in the implementation of functionality for the [DBMS_AWR](DBMS_AWR) advanced package.

> **Caution**:
>
> The advanced package DBMS_AWR_EXTRA can only be invoked internally via the advanced packages DBMS_AWR() or DBMS_WORKLOAD_REPOSITORY(), and direct invocation by users is not permitted.

## UPDATE_SQLSTAT_LAST_SNAP

```plsql
DBMS_AWR_EXTRA.UPDATE_SQLSTAT_LAST_SNAP();
```

The UPDATE_SQLSTAT_LAST_SNAP subroutine is a stored procedure with no parameters, used to update the last snap value of each SQL's statistics in the plan cache to the current value of the corresponding statistics during snapshot creation.

## UPDATE_ASH_FLUSH_ID

```plsql
DBMS_AWR_EXTRA.UPDATE_ASH_FLUSH_ID(ash_flush_id IN NUMBER)
```

The UPDATE_ASH_FLUSH_ID subprogram is used to update the records in the buffer after flushing to disk, i.e., update the latest sample ID that has been flushed to disk currently and the record ID corresponding to the latest sample ID in the buffer.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| ash_flush_id         | Lastest sample ID for flushing |

## ASH_FLUSH

```plsql
DBMS_AWR_EXTRA.ASH_FLUSH(snap_id IN NUMBER)
```

The DBMS_AWR_EXTRA.ASH_FLUSH subprogram is used to automatically persist ASH records after creating a snapshot.

In YAC/Distributed Cluster Deployment, when this stored procedure is called internally, it will ensure that the corresponding buffers of all instances are flushed to disk as needed.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| snap_id         | Snapshot ID |
