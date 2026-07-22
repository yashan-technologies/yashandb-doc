## 通用描述

CREATE TABLESPACE SET语句用于在存算一体分布式集群部署模式下创建一个新的表空间集。

表空间集是YashanDB存算一体分布式集群部署中的一个逻辑存储单位，用于存储分布表及与分布表相关数据信息，物理上对应了各DN节点上的数据文件。

> **Note**: 
>
> 本文出现的所有CHUNK_NUM，均表示当前存算一体分布式集群部署环境中的Chunk总数量，该值可由建库参数USERS_DATASPACE_SCALE_OUT_FACTOR * 建库时DN组个数计算得到结果，其中，建库参数USERS_DATASPACE_SCALE_OUT_FACTOR在安装过程中配置且后续不可修改，可咨询数据库管理员获得该参数的值。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

在存算一体分布式集群部署中，表空间集的数量需要满足`表空间集的数量 * (CHUNK_NUM + 1) + 表空间的数量 <= 2048`。

## 语句定义

**create tablespace set::=**

```ebnf
= CREATE TABLESPACE SET tablespace_set_name (([databucket_clause] ON dataspace_name (MAXSIZE size_clause [NEXT size_clause] [SIZE size_clause]))|(ON dataspace_name MEMORY MAPPED SIZE size_clause )).
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

### tablespace\_set\_name

该语句用于指定要创建的表空间集的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="databucketclause" name="databucketclause"></span>

### databucket\_clause

指定对象存储目录的路径信息，指定多个路径用`,`分隔，每一个路径表示创建一个bucket（数据桶），可省略，省略则挂载至系统默认生成的bucket中，如不省略则不会默认生成bucket。 

可通过查询GV$DATABUCKET视图查看当前系统中所有创建的bucket信息。

YashanDB中单个表空间集下允许挂载DataBucket数量的最大值为64，数据库中允许挂载Databucket数量的最大值取决于建库参数，可查看[CREATE DATABASE](./CREATE DATABASE)章节中maxdatabuckets语句描述。

指定本语句后，不能再同时指定[MEMORY MAPPED](#memorymapped)关键字。

<span id="bucketclause" name="bucketclause"></span>

#### bucket_clause

该语句用于指定bucket信息，例如路径，大小等。

YashanDB支持创建本地存储bucket及S3（Simple Storage Service） bucket，本地存储bucket将数据存储至本地路径中，S3 bucket为面向互联网的云存储。

> **Note**: 
>
> S3 bucket功能默认不开启，如需使用请联系我们的技术支持处理。

##### bucket_name

对于不同的bucket类型，bucket_name具有不同的含义：

- 对于本地存储的bucket，bucket_name可以被指定为逻辑名称，也可以为在本地文件系统的绝对路径或相对路径，但在数据库中的意义均等同于路径。若只指定bucket名字，将默认在$YASDB_DATA/local_fs目录下创建为该bucket名字的目录。若指定bucket路径，系统将对指定的路径进行以下有效性判断：

  - 指定的目录只允许在$YASDB_DATA/local_fs目录下。

  - 指定的目录不允许与其他bucket相同，也不允许为其父目录或子目录。

  - 指定目录的上级目录必须存在，且系统对指定的目录必须拥有读写权限。

  - 存算一体分布式集群部署中不允许指定绝对路径。

- 对于S3 bucket，bucket_name为逻辑名称，用于在数据库内查询具体的bucket信息。

新创建的bucket名称不可与系统内置bucket重复，否则返回错误，可通过查看GV$DATABUCKET视图获取当前系统中所有的bucket信息。

示例（存算一体分布式集群部署）

```sql
-- 如下语句将新建tbs_tb表空间集，同时：
-- 分别于dn的$YASDB_DATA/local_fs和dbfiles目录下新建lsc_bucket1、lsc_bucket2这两个自定义的数据桶和chunk数个名称为TSS_OID_CHUNK_CHUNKID_FILE_ID的默认数据文件；
-- 分别于mn/cn的$YASDB_DATA/local_fs和dbfiles目录下默认新建名称为TSS_OID_ROOT_DATABUCKET的根数据桶和名称为TSS_OID_ROOT_DATAFILE的根数据文件
CREATE TABLESPACE SET tbs_tb DATABUCKET '?/local_fs/lsc_bucket1','?/local_fs/lsc_bucket2' ON USERS MAXSIZE 8G;
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

用于指定bucket的大小，最小值为1048576（1M），最大值为9223372036854775807。可省略，省略则默认为UNLIMITED。

示例（存算一体分布式集群部署）

```sql
--如下语句将新建s3_tablespace表空间并挂载S3 bucket
CREATE TABLESPACE SET s3_tablespace DATABUCKET 's3_bucket3' S3(URL '192.168.0.1:8000/s3bucket',access key 'ak',secret key 'sk') ON USERS MAXSIZE 8G;
```

### dataspace\_name

该语句用于指定表空间集所属的数据空间，不可省略，目前仅支持的dataspace为users。

<span id="memorymapped" name="memorymapped"></span>

### MEMORY MAPPED

当指定MEMORY MAPPED关键字时，表示所创建表空间集的文件的所有页面都将映射在内存中，YashanDB将此种类型的表空间集命名为内存映射表空间集。

内存映射表空间集支持在数据库启动时采用预加载方式，将表空间集中的数据全部加载到内存中，提高访问性能。

内存映射表空间集创建后无法修改属性。

### SIZE size\_clause

该语句用于指定内存映射表空间集预分配的大小，不可省略。

size\_clause的取值范围为[128 \* DB_BLOCK_SIZE \* CHUNK_NUM, 4GB \* DB_BLOCK_SIZE \* CHUNK_NUM - 1]。

示例（存算一体分布式集群部署）

```sql
CREATE TABLESPACE SET mm_tss
ON USERS
MEMORY MAPPED SIZE 8G;
```

### MAXSIZE size\_clause

该语句用于指定表空间集的最大可扩展空间。该参数对于非内存映射表空间集不可省略，且不能小于预分配的空间大小。该参数不用于内存映射表空间集。

size_clause的取值范围为[128 \* DB_BLOCK_SIZE \* CHUNK_NUM, 4G \* DB_BLOCK_SIZE \* CHUNK_NUM - 1]，当DB_BLOCK_SIZE参数为默认的8K值时，该范围为[1M \* CHUNK_NUM, 32T \* CHUNK_NUM - 1]。

### NEXT size\_clause

该语句用于指定表空间集包含的数据文件每次自动扩展的大小。

size\_clause默认为8192 \* DB_BLOCK_SIZE，用户设置时取值范围为[512 \* DB_BLOCK_SIZE, 32768 \* DB_BLOCK_SIZE]，当DB_BLOCK_SIZE参数为默认的8K值时，该范围为[4M,256M]。

### SIZE size\_clause

该语句用于指定表空间集预分配的大小，可省略，省略则默认按照数据文件及bucket大小进行预分配。

size\_clause的取值范围为[128 \* DB_BLOCK_SIZE \* CHUNK_NUM, MAXSIZE]，MAXSIZE表示当前表空间集的最大可扩展空间，当DB_BLOCK_SIZE参数为默认的8K时，最小值为1M \* CHUNK_NUM。

示例（存算一体分布式集群部署）

```sql
CREATE TABLESPACE SET tss1
ON USERS
MAXSIZE 8G SIZE 7G;
```
