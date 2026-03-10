**Database Operations** (such as order by, hash join, statistics collection, etc.) first compute intermediate results through the database's virtual memory, controlled by the VM_BUFFER_SIZE parameter. However, if the virtual memory is insufficient, it is necessary to swap virtual memory to the SWAP tablespace to free memory, and if needed, swap memory back from the SWAP tablespace.

The SWAP tablespace is a non-persistent tablespace, used solely for swapping in and out of database virtual memory, so persistent objects (such as tables, indexes, etc.) cannot be created in the SWAP tablespace.

When the SWAP tablespace runs low on space, it may cause operations that generate a large number of intermediate results to fail. Therefore, it is essential to reasonably plan the size of the SWAP tablespace or enable auto-extension based on actual business needs.

- In Standalone Deployment, you can use the built-in SWAP tablespace (named SWAP), or you can create a custom SWAP tablespace.

- In YAC Deployment, you can use the built-in SWAP tablespace (named SWAP), or create a custom SWAP tablespace. The custom-created SWAP tablespace can be further divided into a SWAP tablespace shared by all instances and a local SWAP tablespace exclusive to each instance.

- In ISC Distributed Cluster Deployment, only the built-in SWAP tablespace (named SWAP) can be used, and manual creation of SWAP tablespaces is not allowed.

In Standalone Primary-Standby Deployment or Primary-Standby Cluster Deployment, when the primary database/cluster creates a local SWAP tablespace or local SWAP tablespace, the standby database/cluster will synchronously create the corresponding local SWAP tablespace or local SWAP tablespace and a shadow-state temporary file with the same name.
  
 - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

 - After the primary-standby switch, the existing local SWAP tablespace or local SWAP tablespaces in the new primary database/cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local SWAP tablespace or local SWAP tablespace or add new files to the existing local SWAP tablespace or local SWAP tablespace for use.

 - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary database/cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary database/cluster. After other standby database/clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



**Create SWAP Tablespace**

***Example*** for Standalone Deployment

```sql
CREATE SWAP TABLESPACE swap_shared TEMPFILE '?/dbfiles/swap_shared' SIZE 4M;
```

***Example*** for YAC Deployment

```sql
CREATE SWAP TABLESPACE swap_shared TEMPFILE '+DG0/dbfiles/swap_shared' SIZE 4M;
```

**Create Local SWAP Tablespace**

***Example*** for YAC Deployment

```sql
CREATE LOCAL SWAP TABLESPACE swap_local TEMPFILE '?/dbfiles/swap_local' SIZE 4M;
```

**Add Data File to SWAP Tablespace**

***Example*** for Standalone Deployment

```sql
ALTER TABLESPACE swap_shared ADD TEMPFILE 'swap01' SIZE 10M AUTOEXTEND ON;
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE swap_shared ADD TEMPFILE '+DG0/dbfiles/swap_shared01' SIZE 10M AUTOEXTEND ON;
```

> **Note**: 
>
> In YAC Deployment, both SWAP and local SWAP tablespaces support adding files with YFS paths, but only the local SWAP tablespace can add files with local disk paths.

**Adjust Data File Size for SWAP Tablespace**

This functionality applies only to Standalone Deployment.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE TEMPFILE 'swap01' RESIZE 20M;
```

**Delete Data File from SWAP Tablespace**

It is not allowed to delete the data file from the default swap tablespace.

***Example*** for Standalone Deployment

```sql
ALTER TABLESPACE swap_shared DROP TEMPFILE 'swap01';
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE swap_shared DROP TEMPFILE '+DG0/dbfiles/swap_shared01';
```

> **Note**: 
>
> In YAC Deployment, all instances must be online to delete files.

**Delete (Local) SWAP Tablespace**

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP TABLESPACE swap_shared INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;
```

> **Note**: 
>
> In YAC Deployment, all instances must be online to delete the SWAP tablespace.

**Query the Current SWAP Tablespace Name in Use**

***Example***

```sql
show parameter DEFAULT_SWAP_TABLESPACE;
```

**Switch SWAP Tablespace**

***Example***

```sql
ALTER SYSTEM SET DEFAULT_SWAP_TABLESPACE = 'SWAP';
```

**Query SWAP Tablespace Information**

***Example***

```sql
SELECT ID,TABLESPACE_NAME,BLOCK_SIZE,MAX_SIZE/1024/1024 MAX_SIZE,TOTAL_BYTES/1024/1024 TOTAL_SIZE,STATUS,CONTENTS,LOGGING,ALLOCATION_TYPE,
SEGMENT_SPACE_MANAGEMENT,ENCRYPTED FROM DBA_TABLESPACES;

 ID TABLESPACE_NAME   BLOCK_SIZE    MAX_SIZE  TOTAL_SIZE STATUS    CONTENTS    LOGGING       ALLOCATION_TYPE SEGMENT_SPACE_MANAGEMENT ENCRYPTED
 -- --------------- ------------ ----------- ----------- --------- ----------- ------------- --------------- ------------------------ ---------
  0 SYSTEM                  8192      524288          64 ONLINE    PERMANENT   LOGGING       AUTO            BITMAP                   N
  1 SYSAUX                  8192      524288          64 ONLINE    PERMANENT   LOGGING       AUTO            BITMAP                   N
  2 UNDO                    8192       65536          64 ONLINE    UNDO        LOGGING       UNIFORM         BITMAP                   N
  3 TEMP                    8192      524288          94 ONLINE    TEMPORARY   NOLOGGING     AUTO            BITMAP                   N
  4 SWAP                    8192      524288          64 ONLINE    SWAP        NOLOGGING     UNIFORM         BITMAP                   N
  5 USERS                   8192      524288         124 ONLINE    PERMANENT   LOGGING       AUTO            BITMAP                   N
```

**Query Data File Information for SWAP Tablespace**

You can query `DBA_DATA_FILES` and `DBA_TEMP_FILES` views for basic information about the SWAP tablespace data files.

***Example***

```sql
SELECT FILE_NAME,FILE_ID,TABLESPACE_NAME,BYTES,BLOCKS,STATUS,MAXBYTES,MAXBLOCKS,AUTO_EXTEND FROM DBA_DATA_FILES 
WHERE TABLESPACE_NAME='SWAP';

FILE_NAME                                       FILE_ID TABLESPACE_NAME     BYTES       BLOCKS STATUS        MAXBYTES   MAXBLOCKS AUTO_EXTEND 
-------------------------------------------    -------- --------------- --------- ------------ --------- ------------ ----------- ----------- 
/usr/local/yashandb/yasdb_data/dbfiles/swap           4 SWAP             67108864         8192 ONLINE    549755813888    67108864 ON          
/usr/local/yashandb/yasdb_data/dbfiles/swap01         8 SWAP             20971520         2560 ONLINE    549755813888    67108864 ON 

SELECT FILE_ID,FILE_NAME,STATUS,BYTES,BLOCKS,RELATIVE_FNO,AUTOEXTENSIBLE,TABLESPACE_NAME,MAXBYTES,MAXBLOCKS 
FROM DBA_TEMP_FILES WHERE TABLESPACE_NAME='SWAP';

FILE_ID FILE_NAME                                     STATUS     BYTES  BLOCKS RELATIVE_FNO AUTOEXTENSIBLE TABLESPACE_NAME     MAXBYTES  MAXBLOCKS  
------- --------------------------------------------- ------- -------- ------- ------------ -------------- --------------- ------------ ----------  
      4 /usr/local/yashandb/yasdb_data/dbfiles/swap   ONLINE  67108864    8192            0 ON             SWAP            549755813888   67108864 
      8 /usr/local/yashandb/yasdb_data/dbfiles/swap01 ONLINE  20971520    2560            1 ON             SWAP            549755813888   67108864    
```

Additionally, you can query the `V$TEMP_EXTENT_POOL` dynamic view to obtain the current allocation of EXTENT in the SWAP tablespace.

***Example***

```sql
SELECT TABLESPACE_NAME,FILE_ID,EXTENTS_CACHED,EXTENTS_USED,BLOCKS_CACHED,BLOCKS_USED,BYTES_CACHED,BYTES_USED,INTER_FNO
FROM V$TEMP_EXTENT_POOL
WHERE TABLESPACE_NAME='SWAP';

TABLESPACE_NAME                                                       FILE_ID EXTENTS_CACHED EXTENTS_USED BLOCKS_CACHED BLOCKS_USED BYTES_CACHED  BYTES_USED    INTER_FNO
---------------------------------------------------------------- ------------ -------------- ------------ ------------- ----------- ------------ ----------- ------------
SWAP                                                                        5           4747         4747         37976       37976    311099392   311099392            0
SWAP                                                                        6           4592         4592         36736       36736    300941312   300941312            1
```

**Query the Session's Usage of SWAP Tablespace**

You can query the `V$TEMPSEG_USAGE` view to get the usage of temporary segment attributes for each session in the current database.

***Example***

```sql
SELECT USERNAME,USER,SID,SESSION_NUM,SQL_ID,SQLHASH,TABLESPACE,CONTENTS,SEGTYPE,SEGFILE#,SEGBLK#,EXTENTS,BLOCKS,SEGRFNO#,TS#
FROM V$TEMPSEG_USAGE
WHERE TABLESPACE='SWAP';

USERNAME                                                         USER                                                                  SID  SESSION_NUM SQL_ID                          SQLHASH TABLESPACE                                                       CONTENTS      SEGTYPE                   SEGFILE#      SEGBLK#               EXTENTS                BLOCKS     SEGRFNO#      TS# 
---------------------------------------------------------------- ---------------------------------------------------------------- -------- ------------ ----------------- --------------------- ---------------------------------------------------------------- ------------- --------------------- ------------ ------------ --------------------- --------------------- ------------ -------- 
SYS                                                              SYS                                                                    69            1 7zgv3dcghff5u                3909568627 SWAP                                                             TEMPORARY     SORT                             3           -1                  1627                  1627            0        3
```
