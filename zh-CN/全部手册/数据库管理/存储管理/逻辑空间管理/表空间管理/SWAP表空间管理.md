数据库操作（例如order by，hash join，统计信息收集等）首先会通过数据库虚拟内存（通过VM_BUFFER_SIZE参数控制）缓存计算的中间结果，但如果虚拟内存不足时，需要通过将虚拟内存交换到SWAP表空间来释放内存，必要时再将内存从SWAP表空间换入。

SWAP表空间是非持久化表空间，只用于数据库虚拟内存的换入换出，因此持久化对象（例如表、索引等）不能创建在SWAP表空间。

SWAP表空间的空间不足时，可能会导致产生大量中间结果的操作失败，因此需要根据实际业务合理规划SWAP表空间大小或打开自动扩展。

- 在单机部署中，可以使用内置的SWAP表空间（名称为SWAP），也可以自定义创建SWAP表空间。

- 在共享集群/分布式集群部署中，可以使用内置的SWAP表空间（名称为SWAP），也可以自定义创建SWAP表空间，其中自定义创建的SWAP表空间又分为各实例共享的SWAP表空间以及各实例独享的本地SWAP表空间。

- 在存算一体分布式集群部署中，只能使用内置的SWAP表空间（名称为SWAP），无法手动创建SWAP表空间。

在单机主备、主备集群部署中，主库/主集群创建SWAP表空间或本地SWAP表空间时，备库/备集群会同步创建相应的SWAP表空间或本地SWAP表空间和影子状态的同名临时文件。
  
 - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$DATAFILE的SHADOW字段获取其相关信息。

 - 主备切换后，新主中的存量SWAP表空间或本地SWAP表空间会因其存量文件均为影子状态而无法使用，需创建新的SWAP表空间或本地SWAP表空间（推荐）或为存量SWAP表空间或本地SWAP表空间添加新的文件以供使用。

 - 如需删除影子文件，需确保目标文件的创建者为当前主库/主集群（若不是，需先切换）并在该主库/主集群上执行DROP TEMPFILE语句，其他备库/备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



**创建SWAP表空间**

示例（单机部署）

```sql
CREATE SWAP TABLESPACE swap_shared TEMPFILE '?/dbfiles/swap_shared' SIZE 4M;
```

示例（共享集群/分布式集群部署）

```sql
CREATE SWAP TABLESPACE swap_shared TEMPFILE '+DG0/dbfiles/swap_shared' SIZE 4M;
```

**创建本地SWAP表空间**

示例（共享集群/分布式集群部署）

```sql
CREATE LOCAL SWAP TABLESPACE swap_local TEMPFILE '?/dbfiles/swap_local' SIZE 4M;
```

**为SWAP表空间添加数据文件**

示例（单机部署）

```sql
ALTER TABLESPACE swap_shared ADD TEMPFILE 'swap01' SIZE 10M AUTOEXTEND ON;
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE swap_shared ADD TEMPFILE '+DG0/dbfiles/swap_shared01' SIZE 10M AUTOEXTEND ON;
```

> **Note**: 
>
> 在共享集群/分布式集群部署中，SWAP表空间仅支持添加YFS路径的文件；本地SWAP表空间支持添加本地磁盘路径的文件或YFS路径的文件，但不允许同一个表空间同时存在YFS路径与本地磁盘路径。

**为SWAP表空间调整数据文件大小**


此功能不适用于存算一体分布式集群部署。


在共享集群/分布式集群部署中，本地SWAP表空间的数据文件大小调整无法跨实例操作，即只能RESIZE当前实例所在服务器上的本地文件。

示例（单机、共享集群/分布式集群部署）

```sql
ALTER DATABASE TEMPFILE 'swap01' RESIZE 20M;
```

示例（共享集群/分布式集群部署）

```sql
ALTER DATABASE TEMPFILE '+DG0/dbfiles/swap_shared01' RESIZE 20G;
```


**为SWAP表空间删除数据文件**

不允许删除默认SWAP表空间的数据文件。

示例（单机部署）

```sql
ALTER TABLESPACE swap_shared DROP TEMPFILE 'swap01';
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE swap_shared DROP TEMPFILE '+DG0/dbfiles/swap_shared01';
```

> **Note**: 
>
> 共享集群/分布式集群部署中，必须全部实例在线才能删除文件。

**删除（本地）SWAP表空间**

示例（单机、共享集群/分布式集群部署）

```sql
DROP TABLESPACE swap_shared INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;
```

> **Note**: 
>
> 共享集群/分布式集群部署中，必须全部实例在线才能删除SWAP表空间。

**查询当前正在使用的SWAP表空间名称**

示例

```sql
show parameter DEFAULT_SWAP_TABLESPACE;
```

**切换SWAP表空间**

示例

```sql
ALTER SYSTEM SET DEFAULT_SWAP_TABLESPACE = 'SWAP';
```

**查询SWAP表空间信息**

示例

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

**查询SWAP表空间的数据文件信息**

可以查询`DBA_DATA_FILES`和`DBA_TEMP_FILES`视图获取SWAP表空间数据文件的基本信息。

示例

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

此外，还可查询`V$TEMP_EXTENT_POOL`动态视图获取当前SWAP表空间的EXTENT分配情况。

示例

```sql
SELECT TABLESPACE_NAME,FILE_ID,EXTENTS_CACHED,EXTENTS_USED,BLOCKS_CACHED,BLOCKS_USED,BYTES_CACHED,BYTES_USED,INTER_FNO
FROM V$TEMP_EXTENT_POOL
WHERE TABLESPACE_NAME='SWAP';

TABLESPACE_NAME                                                       FILE_ID EXTENTS_CACHED EXTENTS_USED BLOCKS_CACHED BLOCKS_USED BYTES_CACHED  BYTES_USED    INTER_FNO
---------------------------------------------------------------- ------------ -------------- ------------ ------------- ----------- ------------ ----------- ------------
SWAP                                                                        5           4747         4747         37976       37976    311099392   311099392            0
SWAP                                                                        6           4592         4592         36736       36736    300941312   300941312            1
```

**查询会话对于SWAP表空间的使用情况**

可以通过查询`V$TEMPSEG_USAGE`视图获取当前数据库各会话的临时属性SEGMENT的使用情况。

示例

```sql
SELECT USERNAME,USER,SID,SESSION_NUM,SQL_ID,SQLHASH,TABLESPACE,CONTENTS,SEGTYPE,SEGFILE#,SEGBLK#,EXTENTS,BLOCKS,SEGRFNO#,TS#
FROM V$TEMPSEG_USAGE
WHERE TABLESPACE='SWAP';

USERNAME                                                         USER                                                                  SID  SESSION_NUM SQL_ID                          SQLHASH TABLESPACE                                                       CONTENTS      SEGTYPE                   SEGFILE#      SEGBLK#               EXTENTS                BLOCKS     SEGRFNO#      TS# 
---------------------------------------------------------------- ---------------------------------------------------------------- -------- ------------ ----------------- --------------------- ---------------------------------------------------------------- ------------- --------------------- ------------ ------------ --------------------- --------------------- ------------ -------- 
SYS                                                              SYS                                                                    69            1 7zgv3dcghff5u                3909568627 SWAP                                                             TEMPORARY     SORT                             3           -1                  1627                  1627            0        3
```
