General Description
----

SAVEPOINT is used to establish a savepoint during the transaction process. In the ROLLBACK statement, this savepoint can be used to specify the rollback of operations that occurred after it.

If the transaction has executed a COMMIT statement, the rollback operation including the specified savepoint will be invalidated.

If the name of the savepoint is duplicated, the old savepoint will be overwritten and become invalid.

If a rollback is performed to a particular savepoint, any savepoints defined after it will be cleared.

Statement Definition
----

**savepoint::=**

```ebnf+diagram
syntax::= SAVEPOINT name
```

### 1. savepoint\_name

This statement is used to specify the name of the savepoint, which is not optional and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

Creating savepoints that start with `SYS_S_` is not allowed in ISC Distributed Cluster Deployment.

***Example***

```sql
-- Initial data
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01';
AREA_NO AREA_NAME                                                     DHQ                 
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                        Shanghai  
  
-- Set savepoint
UPDATE area SET DHQ='Hangzhou' WHERE area_no='01';
SAVEPOINT sa_area_1;
UPDATE area SET DHQ='Nanjing' WHERE area_no='01';
SAVEPOINT sa_area_1;
UPDATE area SET DHQ='Suzhou' WHERE area_no='01';
SAVEPOINT sa_area_3;
  
-- Rollback to savepoint, the first savepoint is invalid due to name duplication
ROLLBACK TO SAVEPOINT sa_area_1;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01';
AREA_NO AREA_NAME                                                     DHQ                 
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                        Nanjing       
  
-- Since the rollback to the previous savepoint has been executed, sa_area_3 is cleared
ROLLBACK TO SAVEPOINT sa_area_3;
YAS-02022 savepoint sa_area_3 not exist
```
