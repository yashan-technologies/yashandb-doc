The TEMP tablespace is mainly used for segment allocation of temporary tables and allocation of undo space related to temporary tables, and it stores temporary table data and associated undo information.

Temporary tables and their related undo records are stored in the database's temporary tablespace and do not generate redo information. This reduces the amount of undo stored in the UNDO tablespace and decreases the size of the database redo logs, enhancing database performance.

- In Standalone Deployment, you can use the built-in temporary tablespace (named TEMP), or you can create a custom temporary tablespace.

- In YAC Deployment, you can use the built-in temporary tablespace (named TEMP), or create a custom temporary tablespace. The custom-created temporary tablespace can be further divided into a temporary tablespace shared by all instances and a local temporary tablespace exclusive to each instance.

- In ISC Distributed Cluster Deployment, only the built-in temporary tablespace (named TEMP) can be used, and manual creation of temporary tablespaces is not allowed.

In Standalone Primary-Standby Deployment or Primary-Standby Cluster Deployment, when the primary database/cluster creates a local temporary tablespace or local temporary tablespace, the standby database/cluster will synchronously create the corresponding local temporary tablespace or local temporary tablespace and a shadow-state temporary file with the same name.
  
 - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

 - After the primary-standby switch, the existing local temporary tablespace or local temporary tablespaces in the new primary database/cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local temporary tablespace or local temporary tablespace or add new files to the existing local temporary tablespace or local temporary tablespace for use.

 - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary database/cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary database/cluster. After other standby database/clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



**Creating TEMP Tablespace**

***Example*** for Standalone Deployment

```sql
CREATE TEMPORARY TABLESPACE temp_shared TEMPFILE '?/dbfiles/temp_shared' SIZE 4M;
```

***Example*** for YAC Deployment

```sql
CREATE TEMPORARY TABLESPACE temp_shared TEMPFILE '+DG0/dbfiles/temp_shared' SIZE 4M;
```

**Creating Local TEMP Tablespace**

***Example*** for YAC Deployment

```sql
CREATE LOCAL TEMPORARY TABLESPACE FOR ALL temp_local TEMPFILE '?/dbfiles/temp_local' SIZE 4M;
```

**Adding Data File to TEMP Tablespace**

***Example***

```sql
ALTER TABLESPACE temp ADD TEMPFILE 'temp01' SIZE 10M AUTOEXTEND ON;
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE temp_shared ADD TEMPFILE '+DG0/dbfiles/temp_shared01' SIZE 10M AUTOEXTEND ON;
```

> **Note**: 
>
> In YAC Deployment, both TEMP tablespace and local TEMP tablespace support adding files with YFS paths, but only the local TEMP tablespace can add files with local disk paths.

**Adjusting Data File Size for TEMP Tablespace**

This functionality is only applicable to Standalone Deployment.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE TEMPFILE 'temp01' RESIZE 20M;
```

**Deleting Data File from TEMP Tablespace**

***Example***

```sql
ALTER TABLESPACE temp DROP TEMPFILE 'temp01';
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE temp_shared DROP TEMPFILE '+DG0/dbfiles/temp_shared01';
```

**Dropping TEMP Tablespace**

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP TABLESPACE temp_shared INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;
```

**Viewing Tempfile Information**

You can query the `DBA_TEMP_FILES` view to obtain basic information about the data files in the temporary tablespace.

>**Note**:
>
> This document takes Standalone Deployment as an example; the methods of viewing in different deployment forms are the same, but the $YASDB_DATA/dbfiles/temp path differs. Please refer to the actual output for specifics.

***Example***

```sql
SELECT FILE_ID,FILE_NAME,STATUS,BYTES/1024/1024,AUTOEXTENSIBLE,TABLESPACE_NAME,MAXBYTES/1024/1024,INCREMENT_BY FROM DBA_TEMP_FILES;

     FILE_ID FILE_NAME                                         STATUS    BYTES/1024/1024 AUTOEXTENSIBLE TABLESPACE_NAME    MAXBYTES/1024/1024 INCREMENT_BY
------------ ------------------------------------------------- --------- --------------- -------------- ------------------ ------------------ ------------
           3 /data/yashan/yasdb_data/db-1-1/dbfiles/temp     ONLINE                 64 ON             TEMP                           524288         8192
           4 /data/yashan/yasdb_data/db-1-1/dbfiles/swap     ONLINE                 64 ON             SWAP                           524288         8192
```

Additionally, you can query the `V$TEMP_EXTENT_POOL` dynamic view to obtain the EXTENT allocation status of the current TEMP tablespace.

***Example***

```sql
SELECT TABLESPACE_NAME,FILE_ID,EXTENTS_CACHED,EXTENTS_USED,BLOCKS_CACHED,BLOCKS_USED,BYTES_CACHED,BYTES_USED,INTER_FNO
FROM V$TEMP_EXTENT_POOL
WHERE TABLESPACE_NAME='TEMP';

TABLESPACE_NAME                                                       FILE_ID EXTENTS_CACHED EXTENTS_USED BLOCKS_CACHED BLOCKS_USED BYTES_CACHED  BYTES_USED    INTER_FNO
---------------------------------------------------------------- ------------ -------------- ------------ ------------- ----------- ------------ ----------- ------------
TEMP                                                                        2             64           64           512         512      4194304     4194304            0
TEMP                                                                        3              0            0             0           0            0           0            1
TEMP                                                                        4              0            0             0           0            0           0            2
```
