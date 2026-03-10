The USERS tablespace is the default user tablespace, used to store permanent user objects and private information.

When creating a user without specifying a tablespace, all information under that user, except for sharded tables, will be placed in the USERS tablespace. Information related to sharded tables is stored in the [Tablespace Set](../Tablespace Set Management/00Tablespace Set Management).

**Add Data File to USERS Tablespace**

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE users ADD DATAFILE '?/dbfiles/users02' SIZE 100M;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER TABLESPACE users ADD DATAFILE '+DG0/dbfiles/users02' SIZE 100M;
```

**Resize Data File in USERS Tablespace**


This functionality is not available to ISC Distributed Cluster Deployment.


***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users02' RESIZE 50M;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE DATAFILE '+DG0/dbfiles/users02' RESIZE 20G;
```

**Drop Data File from USERS Tablespace**

***Example*** for Standalone Deployment

```sql
ALTER TABLESPACE users DROP DATAFILE '?/dbfiles/users02';
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER TABLESPACE users DROP DATAFILE '+DG0/dbfiles/users02';
```

> **Note**: 
>
> When specifying the data file path in the above statements, in Standalone Deployment, you can specify either an absolute or relative path. In ISC Distributed Cluster Deployment, only relative paths are allowed, and in YAC/Distributed Cluster Deployment, the paths must be absolute and can only be YFS paths.

**View Object Information Using USERS Tablespace**

***Example***

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE, SUM(BYTES)/1024/1024 SIZE_M FROM DBA_SEGMENTS D 
WHERE D.TABLESPACE_NAME = 'USERS'
GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME            SEGMENT_NAME         SEGMENT_TYPE           SIZE_M
-------------------------- -------------------- ----------------- -----------
USERS                      SYS_C_21             INDEX                    .125
USERS                      AREA                 TABLE                    .125
```
