Local cache tablespaces are a tablespace type unique to YAC/Distributed Cluster Deployment, used to store local disk cache data for LSC tables, which can effectively improve the query performance of LSC tables.

The local cache tablespace is a persistent one. Restarting the database won't clear the corresponding data. However, the reliability of the cache isn't guaranteed. (The cache might get corrupted, and in that case, the data will be reread from the source file.)

The usage rules for local cache tablespaces are as follows:

- The file path of the local cache tablespace must be a local path. If only a relative path is specified, it defaults to the $YASDB_DATA/dbfiles directory.

- Only one local cache tablespace is allowed per cluster, and the name must be `cache` (case-insensitive).

- In primary-standby cluster deployment, the standby cluster does not synchronize when the primary cluster creates a local cache tablespace.

- Local cache tablespaces are only used as disk cache for LSC tables. Tables cannot be created in a local cache tablespace.

- Local cache tablespaces do not support the following features:

    - Encryption

    - Compression
    - DataBucket mounting

- Only after creating a local cache tablespace can you enable the local disk cache function for LSC tables through the [ENABLE_DISKCACHE](../../../../Reference Manual/Configuration Parameters.md#enable_diskcache) parameter.

- Before deleting a local cache tablespace, ensure that disk cache is disabled (ENABLE_DISKCACHE = FALSE).

**Create Local Cache Tablespace**

***Example*** for YAC/Distributed Cluster Deployment

```sql
CREATE LOCAL CACHE TABLESPACE cache DATAFILE '?/dbfiles/local_cache' SIZE 128M;
```

**Add Data File to Local Cache Tablespace**

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER TABLESPACE cache ADD DATAFILE '?/dbfiles/local_cache02' SIZE 128M;
```

> **Note**:
>
> Local cache tablespaces only support adding data files with local disk paths, not YFS paths or TEMPFILE.

**Delete Data File from Local Cache Tablespace**

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER TABLESPACE cache DROP DATAFILE '?/dbfiles/local_cache02';
```

**Query Local Cache Tablespace Information**

***Example***

```sql
SELECT ID,TABLESPACE_NAME,BLOCK_SIZE,MAX_SIZE/1024/1024 MAX_SIZE,TOTAL_BYTES/1024/1024 TOTAL_SIZE,STATUS,CONTENTS,LOGGING,ALLOCATION_TYPE,
SEGMENT_SPACE_MANAGEMENT,ENCRYPTED FROM DBA_TABLESPACES WHERE TABLESPACE_NAME='CACHE';

 ID TABLESPACE_NAME   BLOCK_SIZE    MAX_SIZE  TOTAL_SIZE STATUS    CONTENTS    LOGGING       ALLOCATION_TYPE SEGMENT_SPACE_MANAGEMENT ENCRYPTED
 -- --------------- ------------ ----------- ----------- --------- ----------- ------------- --------------- ------------------------ ---------
  7 CACHE                   8192      524288         128 ONLINE    CACHE       LOGGING       AUTO            BITMAP                   N
```

**Query Data File Information for Local Cache Tablespace**

You can query the `DBA_DATA_FILES` view for basic information about the local cache tablespace data files.

***Example***

```sql
SELECT FILE_NAME,FILE_ID,TABLESPACE_NAME,BYTES,BLOCKS,STATUS,MAXBYTES,MAXBLOCKS,AUTO_EXTEND FROM DBA_DATA_FILES
WHERE TABLESPACE_NAME='CACHE';

FILE_NAME                                              FILE_ID  TABLESPACE_NAME     BYTES       BLOCKS STATUS        MAXBYTES   MAXBLOCKS AUTO_EXTEND
---------------------------------------------------    -------- --------------- --------- ------------ --------- ------------ ----------- -----------
/data/yashan/yasdb_data/ce-1-1/dbfiles/local_cache     7         CACHE          134217728        16384 ONLINE       134217728       16384 OFF
```

**Query Disk Cache Usage**

You can query the `V$DISKCACHE` dynamic view to check the usage of local disk cache.

***Example***

```sql
SELECT * FROM V$DISKCACHE;
```

**Delete Local Cache Tablespace**

> **Caution**:
>
> Before deleting a local cache tablespace, ensure that disk cache is disabled (ENABLE_DISKCACHE = FALSE), otherwise an error will be reported.

***Example*** for YAC/Distributed Cluster Deployment

```sql
DROP TABLESPACE cache INCLUDING CONTENTS AND DATAFILES;
```