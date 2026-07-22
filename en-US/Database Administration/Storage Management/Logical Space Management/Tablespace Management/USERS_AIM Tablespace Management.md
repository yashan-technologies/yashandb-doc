The USERS_AIM tablespace is a distributed built-in memory mapped tablespace. Memory mapped tablespaces have all pages of the file mapped into memory when the system is running.

The USERS_AIM tablespace is only used to store duplicated tables, while sharded tables are stored in the [Tablespace Set](../Tablespace Set Management/00Tablespace Set Management).

**Add Data File to USERS_AIM Tablespace**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE users_aim ADD DATAFILE '?/dbfiles/users01' SIZE 16M;
```

**Delete Data File from USERS_AIM Tablespace**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE users_aim DROP DATAFILE '?/dbfiles/users01';
```

>  **Note**: 
>
>  In the above statements, when specifying the data file path, relative paths are the only ones allowed.

**View Object Information Using USERS_AIM Tablespace**

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE,SUM(BYTES)/1024/1024  SIZE_M FROM DBA_SEGMENTS D 
WHERE D.TABLESPACE_NAME = 'USERS_AIM'
GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME         SEGMENT_NAME         SEGMENT_TYPE           SIZE_M 
----------------------- -------------------- ----------------- ----------- 
USERS_AIM               AREA0                TABLE                  2.4375		
```
