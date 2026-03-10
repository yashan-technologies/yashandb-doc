TEMP临时表空间主要用于临时表的段分配以及与临时表相关undo空间分配，并存储临时表数据和临时表相关的undo。

临时表及其相关的undo记录被存储在数据库的临时表空间中，并且不生成redo信息，从而减少了UNDO表空间中存储的undo数量，也减少了数据库redo日志的大小；提升数据库性能。

- 在单机部署中，可以使用内置的临时表空间（名称为TEMP），也可以自定义创建临时表空间。

- 在共享集群/分布式集群部署中，可以使用内置的临时表空间（名称为TEMP），也可以自定义创建临时表空间，其中自定义创建的临时表空间又分为各实例共享的临时表空间以及各实例独享的本地临时表空间。

- 在存算一体分布式集群部署中，只能使用内置的临时表空间（名称为TEMP），无法手动创建临时表空间。

在单机主备、主备集群部署中，主库/主集群创建临时表空间或本地临时表空间时，备库/备集群会同步创建相应的临时表空间或本地临时表空间和影子状态的同名临时文件。
  
 - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$DATAFILE的SHADOW字段获取其相关信息。

 - 主备切换后，新主中的存量临时表空间或本地临时表空间会因其存量文件均为影子状态而无法使用，需创建新的临时表空间或本地临时表空间（推荐）或为存量临时表空间或本地临时表空间添加新的文件以供使用。

 - 如需删除影子文件，需确保目标文件的创建者为当前主库/主集群（若不是，需先切换）并在该主库/主集群上执行DROP TEMPFILE语句，其他备库/备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



**创建TEMP表空间**

示例（单机部署）

```sql
CREATE TEMPORARY TABLESPACE temp_shared TEMPFILE '?/dbfiles/temp_shared' SIZE 4M;
```

示例（共享集群/分布式集群部署）

```sql
CREATE TEMPORARY TABLESPACE temp_shared TEMPFILE '+DG0/dbfiles/temp_shared' SIZE 4M;
```

**创建本地TEMP表空间**

示例（共享集群/分布式集群部署）

```sql
CREATE LOCAL TEMPORARY TABLESPACE FOR ALL temp_local TEMPFILE '?/dbfiles/temp_local' SIZE 4M;
```

**为TEMP表空间添加数据文件**

示例

```sql
ALTER TABLESPACE temp ADD TEMPFILE 'temp01' SIZE 10M AUTOEXTEND ON;
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE temp_shared ADD TEMPFILE '+DG0/dbfiles/temp_shared01' SIZE 10M AUTOEXTEND ON;
```

> **Note**: 
>
> 在共享集群/分布式集群部署中，TEMP表空间仅支持添加YFS路径的文件；本地TEMP表空间支持添加本地磁盘路径的文件或YFS路径的文件，但不允许同一个表空间同时存在YFS路径与本地磁盘路径。

**为TEMP表空间调整数据文件大小**


此功能不适用于存算一体分布式集群部署。


在共享集群/分布式集群部署中，本地TEMP表空间的数据文件大小调整无法跨实例操作，即只能RESIZE当前实例所在服务器上的本地文件。

示例（单机、共享集群/分布式集群部署）

```sql
ALTER DATABASE TEMPFILE 'temp01' RESIZE 20M;
```

示例（共享集群/分布式集群部署）

```sql
ALTER DATABASE TEMPFILE '+DG0/dbfiles/temp_shared01' RESIZE 20G;
```

**为TEMP表空间删除数据文件**

示例

```sql
ALTER TABLESPACE temp DROP TEMPFILE 'temp01';
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE temp_shared DROP TEMPFILE '+DG0/dbfiles/temp_shared01';
```

**删除TEMP表空间**

示例（单机、共享集群/分布式集群部署）

```sql
DROP TABLESPACE temp_shared INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;
```

**查看tempfile信息**

可以查询`DBA_TEMP_FILES`视图获取临时表空间数据文件基本信息。

>**Note**:
>
>本文以单机部署为例，不同部署形态的查看方法相同但$YASDB_DATA/dbfiles/temp路径不同，具体请以实际返回结果为准。

示例

```sql
SELECT FILE_ID,FILE_NAME,STATUS,BYTES/1024/1024,AUTOEXTENSIBLE,TABLESPACE_NAME,MAXBYTES/1024/1024,INCREMENT_BY FROM DBA_TEMP_FILES;

     FILE_ID FILE_NAME                                         STATUS    BYTES/1024/1024 AUTOEXTENSIBLE TABLESPACE_NAME    MAXBYTES/1024/1024 INCREMENT_BY
------------ ------------------------------------------------- --------- --------------- -------------- ------------------ ------------------ ------------
           3 /data/yashan/yasdb_data/db-1-1/dbfiles/temp     ONLINE                 64 ON             TEMP                           524288         8192
           4 /data/yashan/yasdb_data/db-1-1/dbfiles/swap     ONLINE                 64 ON             SWAP                           524288         8192
```

此外，还可查询`V$TEMP_EXTENT_POOL`动态视图获取当前TEMP表空间的EXTENT分配情况。

示例

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
