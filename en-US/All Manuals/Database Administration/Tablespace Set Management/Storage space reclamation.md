The following method is used to reclaim space.

**Query the space usage of each tablespace set through views**

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT name,SUM(DATAFILES_MAX_SIZE),SUM(DATAFILES_SIZE),SUM(DATAFILES_FREE_BLOCKS),SUM(DATABUCKETS_SIZE) FROM GV$TABLESPACE_SET GROUP BY name;
```

**Shrink Data File**

If a data file in a tablespace set has a large amount of free data blocks, the tablespace set can be shrunk as needed.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users SHRINK space;
```

**Shrink Stable Data File**

If a stable data file in a tablespace set occupies a large amount of space, you can obtain the row count information for each table stored in the slices using GV$LSC_SLICE_STAT and compare it with the actual row count of the table.

When the actual row count of the table is significantly less than the stored row count in stable data, you can organize the stable data of the table as needed. The organization methods include:

- Specify the compact keyword to merge stable data.
- Specify the clean keyword to clean the data that meets the deferred cleaning conditions after the conversion task is completed.

***Example*** for ISC Distributed Cluster Deployment LSC tables

```sql
SELECT OBJECT_NAME,SUM(ROW_COUNT) row_num FROM DBA_OBJECTS, GV$LSC_SLICE_STAT WHERE OBJECT_ID = BO GROUP BY OBJECT_NAME ORDER BY row_num DESC;

OBJECT_NAME                                                          ROW_NUM 
---------------------------------------------------------------- ----------- 
orders_info                                                     106464


SELECT COUNT(*) FROM orders_info;

COUNT(*) 
--------------------- 
                   12


ALTER TABLE orders_info ALTER SLICE ALL compact;


ALTER TABLE orders_info ALTER SLICE ALL clean;

```
