The following method is used to reclaim space.

**Query the space usage of each tablespace set through views**

***Example*** for ISC Distributed Cluster Deployment

```sql
select name,sum(DATAFILES_MAX_SIZE),sum(DATAFILES_SIZE),sum(DATAFILES_FREE_BLOCKS),sum(DATABUCKETS_SIZE) from gv$tablespace_set group by name;
```

**Shrink Data File**

If a data file in a tablespace set has a large amount of free data blocks, the tablespace set can be shrunk as needed.

***Example*** for ISC Distributed Cluster Deployment

```sql
alter tablespace set users shrink space;
```

**Shrink Stable Data File**

If a stable data file in a tablespace set occupies a large amount of space, you can obtain the row count information for each table stored in the slices using gv$lsc_slice_stat and compare it with the actual row count of the table.

When the actual row count of the table is significantly less than the stored row count in stable data, you can organize the stable data of the table as needed. The organization methods include:

- Specify the compact keyword to merge stable data.
- Specify the clean keyword to clean the data that meets the deferred cleaning conditions after the conversion task is completed.

***Example*** for ISC Distributed Cluster Deployment LSC tables

```sql
SQL> select OBJECT_NAME,sum(ROW_COUNT) row_num from dba_objects, gv$lsc_slice_stat where OBJECT_ID = BO group by OBJECT_NAME order by row_num desc;

OBJECT_NAME                                                          ROW_NUM 
---------------------------------------------------------------- ----------- 
orders_info                                                     106464

1 rows fetched.

SQL> select count(*) from orders_info;

COUNT(*) 
--------------------- 
                   12

1 row fetched.

SQL> alter table orders_info alter slice all compact;

Succeed.

SQL> alter table orders_info alter slice all clean;

Succeed.
```
