The SYSAUX tablespace is an auxiliary tablespace for the SYSTEM tablespace and is the default tablespace for many features of YashanDB (such as snapshot information). Its total size is determined by the space occupied by these feature components, which depends on the functionality being used by the feature and the state of the database workload.

The SYSAUX tablespace accommodates the space needed for multiple components, which can reduce the number of tablespaces required by the database and lessen the load on the SYSTEM tablespace.

YashanDB does not allow the deletion or renaming of the SYSAUX tablespace. When the SYSAUX tablespace becomes unavailable due to an exception, core database functionality continues to operate, but the related functionality using the SYSAUX tablespace may fail or be restricted.

If the SYSAUX tablespace is not configured during the default installation, it will grow over time. It is recommended that database administrators perform daily monitoring and regular cleanup.

**Adding Data File to SYSAUX Tablespace**

> **Note**: 
>
> When specifying the data file path, it can be specified as an absolute or relative path in Standalone Deployment, as an absolute path for YAC Deployment (only YFS paths), and only as a relative path in ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SYSAUX ADD DATAFILE '?/dbfiles/sysaux01' SIZE 10G AUTOEXTEND OFF;
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE SYSAUX ADD DATAFILE '+DG0/dbfiles/sysaux01' SIZE 10G AUTOEXTEND OFF;
```

**Deleting Data File from SYSAUX Tablespace**

***Example*** for Standalone Deployment

```sql
ALTER TABLESPACE SYSAUX DROP DATAFILE '?/dbfiles/sysaux01';
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE SYSAUX DROP DATAFILE '+DG0/dbfiles/sysaux01';
```

**Adjusting Data File Size in SYSAUX Tablespace**

This functionality is available only for Standalone Deployment.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/sysaux01' RESIZE 20G;
```

**Querying Space Usage of Tables in SYSAUX Tablespace**

***Example***

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE,SUM(BYTES)/1024/1024 SIZE_M FROM DBA_SEGMENTS D
WHERE D.TABLESPACE_NAME = 'SYSAUX' GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME    SEGMENT_NAME                    SEGMENT_TYPE           SIZE_M
------------------ ------------------------------- ----------------- -----------
SYSAUX             WRH$_SQLTEXT                    TABLE PARTITION             4
SYSAUX             WRH$_SQLSTAT                    TABLE PARTITION             4
SYSAUX             WRH$_SYSSTAT_PK                 INDEX PARTITION          .625
SYSAUX             WRH$_SYSSTAT                    TABLE PARTITION          .625
SYSAUX             WRH$_SYSTEM_EVENT               TABLE PARTITION          .375
SYSAUX             WRH$_MEM_USED_COMP_PK           INDEX PARTITION          .125
SYSAUX             WRH$_OSSTAT_PK                  INDEX PARTITION          .125
SYSAUX             WRH$_SERVICE_WAIT_CLASS_PK      INDEX PARTITION          .125
SYSAUX             WRM$_SNAPSHOT                   TABLE                    .125
SYSAUX             WRH$_OSSTAT                     TABLE PARTITION          .125
SYSAUX             WRH$_SERVICE_WAIT_CLASS         TABLE PARTITION          .125
SYSAUX             WRH$_MEM_USED_COMP              TABLE PARTITION          .125
SYSAUX             WRM$_DATABASE_INSTANCE          TABLE                    .125
SYSAUX             WRM$_WR_CONTROL                 TABLE                    .125
```
