通用描述
----

CREATE TABLESPACE用于创建自定义用户表空间，自定义表空间支持透明加密。

除可按需自定义创建表空间外，YashanDB内置了一批基础表空间，相关介绍请查阅CREATE DATABASE的[tablespace_clause](./CREATE DATABASE.md#tablespaceclause)或[表空间管理](../../../数据库管理/存储管理/逻辑空间管理/表空间管理/00表空间管理.md#build-in)。

> **Note**:
>
> 在存算一体分布式集群部署中执行本语句时，系统会先检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。
>
>在存算一体分布式集群部署中执行本语句过程中，若出现节点故障，恢复措施请查阅[用户表空间管理](../../../数据库管理/存储管理/逻辑空间管理/表空间管理/用户表空间管理)。

在存算一体分布式集群部署中，表空间的数量需要满足`表空间集的数量 * (CHUNK_NUM + 1) + 表空间的数量 <= 2048`。

语句定义
----

**create tablespace::=**

```ebnf
= CREATE (permanent_tablespace_clause | temporary_tablespace_clause | swap_tablespace_clause | cache_tablespace_clause).
```

**[permanent_tablespace_clause](#permanenttablespaceclause)::=**

```ebnf
= [ BIGFILE | SMALLFILE ] TABLESPACE tablespace_name [datafile_clause] [extent_management_clause] [MEMORY MAPPED] [databucket_clause] [encryption_clause] [compress_clause].
```

**[temporary_tablespace_clause](#temporarytablespaceclause)::=**

```ebnf
= (TEMPORARY TABLESPACE | LOCAL TEMPORARY TABLESPACE FOR (ALL | LEAF)) tablespace_name [datafile_clause] [extent_management_clause].
```

**[swap_tablespace_clause](#swaptablespaceclause)::=**

```ebnf
= (SWAP TABLESPACE | LOCAL SWAP TABLESPACE) tablespace_name [datafile_clause].
```

**[cache_tablespace_clause](#cachetablespaceclause)::=**

```ebnf
= LOCAL CACHE TABLESPACE tablespace_name [datafile_clause].
```

**[datafile_clause](#datafileclause)::=**

```ebnf
= ((DATAFILE | TEMPFILE) file_specification {"," file_specification}).
```

**[file\_specification](#filespecification)::=**

```ebnf
= "file_name" SIZE size_clause [AUTOEXTEND (OFF|(ON [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]))] [PARALLEL parallel].
```

**[extent\_management\_clause](#extentmanagementclause)::=**

```ebnf
= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause).
```

**[databucket_clause](#databucketclause)::=**

```ebnf
= DATABUCKET (bucket_clause {"," bucket_clause}).
```

**[bucket_clause](#bucketclause)::=**

```ebnf
= "bucket_name" [s3_bucket_clause] [MAXSIZE size_clause].
```

**[s3_bucket_clause](#s3bucketclause)::=**

```ebnf
= S3 "(" URL "'url'" ["," REGION "region"] "," ACCESS KEY "access_key" "," SECRET KEY "secret_key" ")".
```

**[encryption\_clause](#encryptionclause)::=**

```ebnf
= ENCRYPTION [USING ("'" AES128 "'" | "'" SM4 "'")] ENCRYPT.
```

**[compress\_clause](#compressclause)::=**

```ebnf
= COMPRESS [LZ4|ZSTD].
```

<span id="permanenttablespaceclause" name="permanenttablespaceclause"></span>

### permanent\_tablespace\_clause

该语句用于在数据库中创建一个新的表空间，表空间中的对象存储在数据文件或者数据桶中。

#### BIGFILE | SMALLFILE

仅用于语法兼容，无实际含义。

<span id="temporarytablespaceclause" name="temporarytablespaceclause"></span>

### temporary\_tablespace\_clause

该语句用于指定创建临时表空间，其中包含的数据仅在用户会话期间持续，临时表空间中的对象存储在临时文件中。

在存算一体分布式集群部署中，只能使用内置的临时表空间（名称为TEMP），无法手动创建临时表空间。



#### 临时表空间

临时表空间的创建语法为CREATE TEMPORARY TABLESPACE，所创建的临时文件由所有节点共享使用。

- 临时文件的存放路径：

  - 在单机部署中，临时文件路径须为本地路径，若只指定相对路径则默认存放在$YASDB_DATA/dbfiles目录下。
  
  - 在共享集群/分布式集群部署，临时文件路径须为YFS路径。

- 最终创建的文件数量完全取决于SQL语句的指定。

- 在单机主备、主备集群部署中，主库/主集群创建临时表空间时，备库/备集群会同步创建相应的临时表空间和影子状态的同名临时文件。
  
  - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$datafile的SHADOW字段获取其相关信息。

  - 主备切换后，新主中的存量临时表空间会因其存量文件均为影子状态而无法使用，需创建新的临时表空间（推荐）或为存量临时表空间添加新的文件以供使用。

  - 如需删除影子文件，需确保目标文件的创建者为当前主库/主集群（若不是，需先切换）并在该主库/主集群上执行DROP TEMPFILE语句，其他备库/备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



#### 本地临时表空间

本地临时表空间的创建语法为CREATE LOCAL TEMPORARY TABLESPACE FOR ALL|LEAF，其中FOR ALL|LEAF仅用于兼容，无实际含义。

本地临时表空间仅适用于共享集群/分布式集群部署，创建时会为集群内每一个实例创建独立的临时文件，实例间不共享使用同一份文件。

- 临时文件路径支持本地磁盘路径、YFS路径，但同一个表空间内不允许混用。若创建本地临时表空间时只指定相对路径则默认存放在本地，即$YASDB_DATA/dbfiles目录下。

- 最终创建的文件数量为SQL语句指定数量 * 实例个数。

- 在主备集群部署中，主集群创建本地临时表空间时，备集群会同步创建相应的本地临时表空间和影子状态的同名临时文件。
  
  - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$datafile的SHADOW字段获取其相关信息。

  - 主备切换后，新主集群中的存量本地临时表空间会因其存量文件均为影子状态而无法使用，需创建新的本地临时表空间（推荐）或为存量本地临时表空间添加新的文件以供使用。

  - 如需删除影子文件，需确保目标文件的创建者为当前主集群（若不是，需先切换）并在该主集群上执行DROP TEMPFILE语句，其他备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



示例（单机部署）

```sql
CREATE TEMPORARY TABLESPACE shared_temp TEMPFILE '?/dbfiles/shared_temp.dbf' SIZE 4M;
```

示例（共享集群/分布式集群部署）

```sql
CREATE TEMPORARY TABLESPACE shared_temp TEMPFILE '+DG0/dbfiles/shared_temp.dbf' SIZE 4M;
CREATE LOCAL TEMPORARY TABLESPACE FOR ALL local_temp TEMPFILE '?/dbfiles/local_temp.dbf' SIZE 4M;
```

<span id="swaptablespaceclause" name="swaptablespaceclause"></span>

### swap\_tablespace\_clause

该语句用于指定创建SWAP表空间，其中包含的数据仅在用户会话期间持续，SWAP表空间中的对象存储在临时文件中。

在存算一体分布式集群部署中，只能使用内置的SWAP表空间（名称为SWAP），无法手动创建SWAP表空间。



#### SWAP表空间

SWAP表空间的创建语法为CREATE TEMPORARY TABLESPACE，所创建的临时文件由所有节点共享使用。

- 临时文件的存放路径：

  - 在单机部署中，临时文件路径须为本地路径，若只指定相对路径则默认存放在$YASDB_DATA/dbfiles目录下。
  
  - 在共享集群/分布式集群部署，临时文件路径须为YFS路径。

- 最终创建的文件数量完全取决于SQL语句的指定。

- 在单机主备、主备集群部署中，主库/主集群创建SWAP表空间时，备库/备集群会同步创建相应的SWAP表空间和影子状态的同名临时文件。
  
  - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$datafile的SHADOW字段获取其相关信息。

  - 主备切换后，新主中的存量SWAP表空间会因其存量文件均为影子状态而无法使用，需创建新的SWAP表空间（推荐）或为存量SWAP表空间添加新的文件以供使用。

  - 如需删除影子文件，需确保目标文件的创建者为当前主库/主集群（若不是，需先切换）并在该主库/主集群上执行DROP TEMPFILE语句，其他备库/备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



#### 本地SWAP表空间

本地SWAP表空间的创建语法为CREATE LOCAL TEMPORARY TABLESPACE。

本地SWAP表空间仅适用于共享集群/分布式集群部署，创建时会为集群内每一个实例创建独立的临时文件，实例间不共享使用同一份文件。

- 临时文件路径支持本地磁盘路径、YFS路径，但同一个表空间内不允许混用。若创建本地SWAP表空间时只指定相对路径则默认存放在本地，即$YASDB_DATA/dbfiles目录下。

- 最终创建的文件数量为SQL语句指定数量 * 实例个数。

- 在主备集群部署中，主集群创建本地SWAP表空间时，备集群会同步创建相应的本地SWAP表空间和影子状态的同名临时文件。
  
  - 影子文件只会记录在ctrlfile中，不会在磁盘上生成实体物理文件，可查询V$datafile的SHADOW字段获取其相关信息。

  - 主备切换后，新主集群中的存量本地SWAP表空间会因其存量文件均为影子状态而无法使用，需创建新的本地SWAP表空间（推荐）或为存量本地SWAP表空间添加新的文件以供使用。

  - 如需删除影子文件，需确保目标文件的创建者为当前主集群（若不是，需先切换）并在该主集群上执行DROP TEMPFILE语句，其他备集群收到DROP TEMPFILE的redo日志后会清理影子记录。



示例（单机部署）

```sql
CREATE SWAP TABLESPACE shared_swap TEMPFILE '?/dbfiles/shared_swap' SIZE 4M;  
```

示例（共享集群/分布式集群部署）

```sql
CREATE SWAP TABLESPACE shared_swap TEMPFILE '+DG0/dbfiles/shared_swap' SIZE 4M;
CREATE LOCAL SWAP TABLESPACE local_swap TEMPFILE '?/dbfiles/local_swap' SIZE 4M;
```

<span id="cachetablespaceclause" name="cachetablespaceclause"></span>

### cache\_tablespace\_clause

该语句用于在共享集群/分布式集群部署中创建本地缓存表空间，存储LSC表的本地磁盘缓存数据，提升LSC表的查询性能。

本地缓存表空间仅适用于共享集群/分布式集群部署，创建时会为集群内每一个实例创建独立的数据文件。本地缓存表空间的数据会进行持久化，重启数据库不会清理对应数据，但不保证缓存的可靠性（缓存可能损坏，损坏后则从源文件重新读取）。

- 该语句仅适用于共享集群/分布式集群部署。

- 文件路径须为本地路径，若只指定相对路径则默认存放在$YASDB_DATA/dbfiles目录下。

- 每个集群仅允许创建一个本地缓存表空间，且名称必须为`cache`（大小写不敏感）。

- 在主备集群部署中，主集群创建本地缓存表空间时，备集群不会同步。

- 本地缓存表空间仅作为LSC表的磁盘缓存使用，无法在该表空间中创建表。


示例（共享集群/分布式集群部署）

```sql
CREATE LOCAL CACHE TABLESPACE cache DATAFILE '?/dbfiles/local_cache' SIZE 128M;
```

### tablespace\_name

该语句用于指定要创建的表空间的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

- 在共享集群/分布式集群部署中，本地缓存表空间的名称必须指定为`cache`（大小写不敏感）。

- 在存算一体分布式集群部署中，创建的表空间的名称不可与已有的表空间集中分配的表空间名称相同，否则会返回错误。


示例

```sql
--使用所有的默认选项创建一个表空间
CREATE TABLESPACE yashan;
```

<span id="datafileclause" name="datafileclause"></span>

### datafile_clause

该语句用于指定创建表空间对应的数据文件，可省略，则系统按如下规则自动创建一个数据文件：

- 文件名称由表空间名称以及数据文件在表空间内的序号组合生成，例如tablespace_name1，tablespace_name2...，且统一转换为大写。
- 文件的默认大小为8192个BLOCK，文件路径为系统默认的数据文件路径。
- 对于非MEMORY MAPPED表空间，默认文件开启自动扩展，next为8192个块，maxsize为64MB个块。
- 如果没有显式的规定extent分配方式，extent的默认分配方式为系统自动分配。
- 只能使用TEMPFILE关键字创建临时表空间或SWAP表空间。
- 对于本地临时表空间以及本地SWAP表空间而言，添加一个文件等于添加一组文件，每一组文件数量的个数等于集群实例个数。

<span id="filespecification" name="filespecification"></span>

#### file_specification

指定多个数据文件用`,`分隔，可省略，则系统将在默认的数据文件路径下创建一个以表空间名称（转换为大写）命名，大小为8192个块，开启自动扩展，每次扩展8192个块，最大可扩展到64MB个块的数据文件。

可以通过PARALELL指定创建数据文件的并行度，取值范围为1到8。

如果未指定并行度数据库会根据文件大小自适应并行度，文件不超过1G时并行度为1，文件超过128G并行度为8，文件大小在1G到128G之间时并行度为4。

##### file\_name

指定数据文件的名称。

- 在单机部署中，可以采用零路径（例如`yashan`）、绝对路径（例如`/data/yashan/yasdb_data/db-1-1/dbfiles/yashan`）或以`?`或`.`替代$YASDB_DATA的相对路径（例如`?/dbfiles/yashan`或`./dbfiles/yashan`）。采用零路径或相对路径时，实际创建文件时取取系统默认的数据文件路径$YASDB_DATA/dbfiles。

- 在共享集群/分布式集群部署中，不同类型的文件存储路径要求不同：

  - DATAFILE：
  
    - 本地缓存表空间：必须为本地磁盘路径，指定规则同单机部署。

    - 其他：必须为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径，可以采用零路径（例如`yashan`）或完整的磁盘组路径（例如`+DG0/dbfiles/yashan`）。采用零路径时，实际创建文件时取默认的磁盘组路径+DG0/dbfiles。

  - TEMPFILE：

    - 临时表空间以及SWAP表空间的临时文件：必须为YFS路径，指定规则同DATAFILE。

    - 本地临时表空间以及本地SWAP表空间的临时文件：支持YFS路径或本地磁盘路径，其中YFS路径指定规则同DATAFILE，本地磁盘路径指定规则同单机部署。

- 在存算一体分布式集群部署中，可以采用零路径（例如`yashan`）或以`?`或`.`替代$YASDB_DATA的相对路径（例如`?/dbfiles/yashan`或`./dbfiles/yashan`），实际创建文件时取系统默认的数据文件路径$YASDB_DATA/dbfiles。

##### size

指定数据文件的大小，遵循通用[size_clause](../通用SQL语法/size_clause)定义。

单个数据文件最少具有128个块用于存储元信息。

UNDO表空间内的单个数据文件最多具有8MB个块，非UNDO表空间内的单个数据文件最多具有64MB个块。

数据文件的大小等于数据库块大小乘以数据文件块个数，例如当数据库块大小为8K时非UNDO表空间的单个数据文件最小值为1M，最大值为512G。

##### autoextend on|autoextend off

指定此次创建的数据文件是否开启自动扩展，该语句省略时默认为关闭自动扩展。当打开自动扩展时：

*   NEXT size\_clause：数据文件每次自动扩展时的大小由该值指定，以Bytes为单位，取值范围为[512,32768]个块大小，省略时默认为8192个块大小。

*   MAXSIZE UNLIMITED/size\_clause：数据文件可扩展到的最大容量值由该值决定，以Bytes为单位，UNLIMITED表示无限制，省略时默认为64MB个块大小。UNLIMITED表示不限制最大值。

不能对MMS表空间指定AUTOEXTEND ON。

##### parallel

当创建一个较大的数据文件时，可以通过本语句指定并行度，提高创建大文件的速度。不指定本语句时，系统根据文件大小自动选取并行度，例如文件不超过1G时的并行度为1，文件超过128G时的并行度为8，文件大小在1G到128G之间时的并行度为4。

parallel的值应该介于1~8之间。

示例（单机/存算一体分布式集群部署）

```sql
-- 普通路径
CREATE TABLESPACE yashan1 DATAFILE 'yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 2;
```

示例（共享集群/分布式集群部署）

```sql
-- YFS路径
CREATE TABLESPACE yashan1 DATAFILE '+DG0/yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 2;
```

<span id="extentmanagementclause" name="extentmanagementclause"></span>

### extent\_management\_clause

该语句用于指定表空间里的对象申请extent时的空间分配方式，AUTOALLOCATE分配方式根据对象当前大小由系统自动分配extent空间，UNIFORM分配方式每次为对象分配的extent空间为固定值。

*   创建表空间后不能修改extent分配方式。
*   创建临时表空间若不指定extent分配方式，默认使用UNIFORM分配方式，大小为8个BLOCK。
*   创建SWAP表空间不可指定extent分配方式，默认使用UNIFORM分配方式，大小为8个BLOCK。
*   创建非临时表空间若不指定extent分配方式，默认使用AUTOALLOCATE分配方式。
*   创建本地缓存表空间不可指定extent分配方式，默认使用AUTOALLOCATE分配方式。
*   使用UNIFORM分配方式的表空间内的每个数据文件大小都必须大于UNIFORM SIZE。

示例

```sql
CREATE TABLESPACE yashan2 DATAFILE 'yashan2' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G EXTENT UNIFORM SIZE 64K; 
CREATE TABLESPACE yashan3 DATAFILE 'yashan3' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 6 EXTENT UNIFORM SIZE 64K;  
```

### memory mapped

当指定MEMORY MAPPED关键字时，表示所创建表空间的文件的所有页面都将映射在内存中 （AIM：all in memory），YashanDB将此种类型的表空间命名为内存映射表空间（MMS：Memory mapped space）。

共享集群/分布式集群部署下不允许创建MMS。

分布式内存数据库中，自定义表空间时即使不指定MEMORY MAPPED，创建的表空间依然为内存映射表空间mms，所有功能约束与mms保持一致。

MMS表空间支持在数据库启动时采用预加载方式，将表空间数据全部加载到内存中，提高访问性能，具体的预加载策略通过MMS_DATA_LOADERS参数配置。

通过v$tablespace视图的MEMORY_MAPPED字段可查询某个表空间是否为MMS表空间。

存算一体分布式集群部署中，指定了MEMORY MAPPED关键字后，不能再同时指定[databucket_clause](#databucketclause)。

示例（单机、存算一体分布式集群部署）

```sql
CREATE TABLESPACE yashan4 DATAFILE 'yashan4' SIZE 4M EXTENT UNIFORM SIZE 64K MEMORY MAPPED; 
```

<span id="databucketclause" name="databucketclause"></span>

### databucket_clause

指定对象存储目录的路径信息，指定多个路径用`,`分隔，每一个路径表示创建一个bucket（数据桶），可省略，省略则默认创建的表空间不挂载bucket。

可通过查询V$DATABUCKET视图查看当前系统中所有创建的bucket信息。

- 本地缓存表空间无法挂载DataBucket。

- YashanDB中单个表空间下允许挂载DataBucket数量的最大值为64，数据库中允许挂载Databucket数量的最大值取决于建库参数，可查看[CREATE DATABASE](./CREATE DATABASE)章节中maxdatabuckets语句描述。

- LSC表所属表空间必须先挂载数据桶才能创建LSC表。

<span id="bucketclause" name="bucketclause"></span>

#### bucket_clause

该语句用于指定bucket信息，例如路径，大小等。

YashanDB支持为表空间集创建本地存储bucket及S3（Simple Storage Service） bucket，本地存储bucket将数据存储至本地路径中，S3 bucket为面向互联网的云存储。

> **Note**: 
>
> 在共享集群部署、分布式集群部署中，仅支持配置本地存储bucket。
>
> 在单机部署、存算一体分布式集群部署中，S3 bucket功能默认不开启，如需使用请联系我们的技术支持处理。

##### bucket_name

对于不同的bucket类型，bucket_name具有不同的含义：

- 对于本地存储的bucket，bucket_name可以被指定为逻辑名称，也可以为在本地文件系统的绝对路径或相对路径，但在数据库中的意义均等同于路径。若只指定bucket名字，将默认在$YASDB_DATA/local_fs目录（单机部署或存算一体分布式集群部署中）或+DG0/local_fs目录（共享集群或分布式集群部署）下创建为该bucket名字的目录。若指定bucket路径，系统将对指定的路径进行以下有效性判断：

  - 在单机部署或存算一体分布式集群部署中，指定的目录必须在$YASDB_DATA/local_fs目录下。在共享集群或分布式集群部署中，指定的目录必须是YFS路径。

  - 指定的目录不允许与其他bucket相同，也不允许为其父目录或子目录。

  - 指定目录的上级目录必须存在，且系统对指定的目录必须拥有读写权限。

  - 存算一体分布式集群部署中不允许指定绝对路径。

- 对于S3 bucket，bucket_name为逻辑名称，必须由字母、数字或下划线组成，用于在数据库内查询具体的bucket信息。

示例

```sql
-- 如下语句将新建lsc_tb表空间，同时于$YASDB_DATA/local_fs目录下新建lscfile1、lscfile2两个bucket，同时也将默认新建一个名称为LSC_TB0的数据文件
CREATE TABLESPACE lsc_tb DATABUCKET '?/local_fs/lscfile1','?/local_fs/lscfile2';
```

<span id="s3bucketclause" name="s3bucketclause"></span>

##### s3_bucket_clause

该语句用于指定S3 bucket的访问信息。

###### url

用于指定访问地址，不可省略，最大值为1023字节。

###### region

用于指定S3 bucket的物理所属区域，可省略，省略则默认使用对象存储服务的默认配置，最大值为127字节。

###### access key

用于标识客户端身份，不可省略，最大值为127字节。

###### secret key

访问密钥，不可省略，最大值为127字节。

##### MAXSIZE size_clause

用于指定bucket的大小，最小值为1048576（1M），最大值为9223372036854775807。可省略，省略则默认为UNLIMITED。共享集群/分布式集群部署下MAXSIZE只允许指定为UNLIMITED。

示例（单机部署）

```sql
-- 如下语句将新建s3_tablespace表空间并挂载S3 bucket
CREATE TABLESPACE s3_tablespace DATABUCKET 's3_bucket3' S3(URL '192.168.0.1:8000/s3bucket',access key 'ak',secret key 'sk') MAXSIZE 8G;
```

<span id="encryptionclause" name="encryptionclause"></span>

### encryption_clause

该语句用于指定表空间加密属性，以ENCRYPTION开头。本语句可省略，表示创建的表空间不具备加密属性。

加密表空间的使用规则如下：

- 在单机/共享集群/分布式集群部署中，创建加密表空间前必须先完成密钥管理相关配置，包括创建钱包、开启钱包、设置主密钥等，具体操作请查阅[配置钱包](../../../产品安全/加密支持/存储加密/密钥管理.md#configuringwallet)。
- 不能将内置表空间、临时表空间、本地缓存表空间指定为加密表空间。
- 对于加密表空间的表对象，在其上创建的索引和AC也必须位于某个加密表空间。
- 加密属性在创建表空间时指定，后续不可更改。

对于分区表，系统并不限制各分区所在的表空间加密属性是否一致，即允许只对表的部分数据进行加密。在更新操作引发的数据分区移动时，该数据将依据新分区的加密属性按明文或密文存储。

**ENCRYPT**

对表空间开启加密功能。指定为ENCRYPT时，表示创建的表空间为加密表空间，即该表空间对应存储介质上的数据将被加密。

**USING encryption_algorithm**

该语句用于指定加密算法，支持AES128和SM4，可省略，省略时默认采用SM4算法。

> **Note**:
>
> 如需使用国密算法SM4、数据透明加密相关的密钥管理功能，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备.md)检查并确保服务器系统中已安装符合要求的工具。

示例

```sql
-- 如下语句将新建表空间，并指定为加密表空间
CREATE TABLESPACE encrypt_tb ENCRYPTION ENCRYPT;
-- 如下语句将新建加密表空间，并指定采用AES128加密算法加密。
CREATE TABLESPACE aes128_tb ENCRYPTION USING 'AES128' ENCRYPT;
```

<span id="compressclause" name="compressclause"></span>

### compress_clause

该语句用于指定表空间压缩属性，以COMPRESS开头，压缩算法仅支持LZ4和ZSTD，不指定压缩算法默认是LZ4。本语句可省略，表示创建的表空间为非压缩表空间。

指定为COMPRESS时，表示创建的表空间为压缩表空间，即该表空间对应存储介质上的数据将被压缩。YashanDB对压缩表空间的约束规则如下：

- 不能将内置表空间指定为压缩表空间。
- 在共享集群/分布式集群部署中，仅创建使用本地磁盘文件的本地临时表空间或本地SWAP表空间时可以指定为压缩表空间，其他场景均不允许指定为压缩表空间。
- 压缩属性在创建表空间时指定，后续不可更改。
- 如果表空间的数据文件所在文件系统不支持punch hole或者其页面大小不是1024、2048或4096，则无法创建压缩表空间。
- 部分文件系统不支持压缩表空间，常见支持的文件系统有：XFS、ext4、Btrfs、tmpfs(5)、gfs2(5)等。


示例（单机、存算一体分布式集群部署）

```sql
-- 如下语句将新建表空间，并指定为压缩表空间
CREATE TABLESPACE cmprss_tb COMPRESS;
CREATE TABLESPACE lz4_tb COMPRESS lz4;
CREATE TABLESPACE zstd_tb COMPRESS zstd;
```
