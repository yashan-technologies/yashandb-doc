通用描述
----

ALTER TABLESPACE SET语句用于更改存算一体分布式集群部署中一个已存在的表空间集的相关属性。

表空间集是YashanDB存算一体分布式集群部署中的一个逻辑存储单位，用于存储分布表及与分布表相关数据信息，物理上对应了各DN节点上的数据文件。

目前只能指定修改非内存映射表空间集。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

> **Note**: 
>
> 本文出现的所有CHUNK_NUM，均表示当前存算一体分布式集群部署环境中的Chunk总数量，该值可由建库参数USERS_DATASPACE_SCALE_OUT_FACTOR * 建库时DN组个数计算得到结果，其中，建库参数USERS_DATASPACE_SCALE_OUT_FACTOR在安装过程中配置且后续不可修改，可咨询数据库管理员获得该参数的值。

语句定义
----

**alter tablespace set::=**

```ebnf+diagram
syntax::= ALTER TABLESPACE SET tablespace_set_name (MAXSIZE size_clause | NEXT size_clause | RESIZE size_clause | shrink_clause | databucket_clause)
```

**[shrink_clause](#shrinkclause)::=**

```ebnf+diagram
syntax::= SHRINK SPACE [KEEP size_clause]
```

**[databucket_clause](#databucketclause)::=**

```ebnf+diagram
syntax::= add_databucket_clause | alter_databucket_clause | drop_databucket_clause
```

**[add_databucket_clause](#adddatabucketclause)::=**

```ebnf+diagram
syntax::= ADD DATABUCKET ((bucket_clause) {"," (bucket_clause)})
```

**[bucket_clause](#bucketclause)::=**

```ebnf+diagram
syntax::= "'bucket_name'" [s3_bucket_clause] [MAXSIZE size_clause]
```

**[s3_bucket_clause](#s3bucketclause)::=**

```ebnf+diagram
syntax::= S3 "(" URL "'url'" ["," REGION "'region'"] "," ACCESS KEY "'access_key'" "," SECRET KEY "'secret_key'" ")"
```

**[alter_databucket_clause](#alterdatabucketclause)::=**

```ebnf+diagram
syntax::= ALTER DATABUCKET "'bucket_name'" (READONLY | READWRITE)
```

**[drop_databucket_clause](#dropdatabucketclause)::=**

```ebnf+diagram
syntax::= DROP DATABUCKET "'bucket_name'"
```

### 1. tablespace\_set\_name

表空间集的名称。

### 2. MAXSIZE size\_clause

该语句用于修改表空间集的最大可扩展空间。

size_clause的取值范围为[128 \* DB_BLOCK_SIZE \* CHUNK_NUM, 4G \* DB_BLOCK_SIZE \* CHUNK_NUM - 1]，当DB_BLOCK_SIZE参数为默认的8K值时，该范围为[1M \* CHUNK_NUM, 32T \* CHUNK_NUM - 1]。

增加表空间集的最大可扩展空间会相应地增加所有DN组上的数据文件的最大可扩展空间：

1. 所有数据文件的最大可扩展空间之和构成了表空间集的最大可扩展空间。
2. 当增加表空间集的最大可扩展空间时，系统将新的最大空间均分给每个DN组，每个DN组先将已有的文件的最大值扩展到最大，然后继续建立新的文件。
3. 新建立的数据文件初始大小默认为1M ，除最后一个文件外的其它文件的最大可扩展空间为最大值。

缩小表空间集的最大可扩展空间则会相应地缩小所有DN组上的数据文件的最大可扩展空间。

示例（存算一体分布式集群部署）

```sql
--查看当前USERS表空间集最大的max_size (TSS_2616为系统默认的users表空间标识符)
SELECT SUM(max_size) FROM GV$DATAFILE WHERE name LIKE '%/TSS_2616%_CHUNK_%' AND GROUP_ID > 2 AND GROUP_NODE_ID = 1;

SUM(MAX_SIZE)
-------------
   2.3090E+13

--将USERS表空间集的maxsize扩大至22T
ALTER TABLESPACE SET users MAXSIZE 22T;
```

### 3. NEXT size\_clause

该语句用于修改表空间集内部的数据文件每次自动扩展的大小。

size_clause的取值范围为[512 \* DB_BLOCK_SIZE, 32768 \* DB_BLOCK_SIZE]，当DB_BLOCK_SIZE参数为默认的8K值时，该范围为[4M,256M]。

示例（存算一体分布式集群部署）

```sql
--接上例
ALTER TABLESPACE SET users NEXT 100M;

--查询修改后的数据文件信息
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=7
ORDER BY 3,1,2;
DN_NODE   FILENAME                     TS#      BYTES RELATIVE_FNO AUTO_EXTEND   NEXT_SIZE       MAX_SIZE 
--------- -------------------------- ----- ---------- ------------ ----------- ----------- -------------- 
3-1       TSS_1800_CHUNK_0_FILE_0        7   68157440            0 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_1        7    1048576            1 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_2        7    1048576            2 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_3        7    1048576            3 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_0        7    1048576            0 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_1        7    1048576            1 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_2        7    1048576            2 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_3        7    1048576            3 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_0        7    1048576            0 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_1        7    1048576            1 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_2        7    1048576            2 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_3        7    1048576            3 ON            104857600   549755813888
																				
```

### 4. RESIZE size\_clause

该语句用于修改表空间集数据文件的大小。

size_clause的取值范围为[128 \* DB_BLOCK_SIZE \* CHUNK_NUM, MAXSIZE]，MAXSIZE表示当前表空间集的最大可扩展空间，当DB_BLOCK_SIZE参数为默认的8K时，最小值为1M \* CHUNK_NUM。

当用户设置的size小于当前实际大小时，将不会进行修改。

示例（存算一体分布式集群部署）

```sql
--接上例
ALTER TABLESPACE SET users RESIZE 300M;

--查询修改后的数据文件信息
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=7
ORDER BY 3,1,2;
DN_NODE   FILENAME                     TS#      BYTES RELATIVE_FNO AUTO_EXTEND   NEXT_SIZE       MAX_SIZE 
--------- -------------------------- ----- ---------- ------------ ----------- ----------- -------------- 
3-1       TSS_1800_CHUNK_0_FILE_0        7  104857600            0 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_1        7    1048576            1 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_2        7    1048576            2 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_3        7    1048576            3 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_0        7  104857600            0 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_1        7    1048576            1 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_2        7    1048576            2 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_3        7    1048576            3 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_0        7  104857600            0 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_1        7    1048576            1 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_2        7    1048576            2 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_3        7    1048576            3 ON            104857600   549755813888
																				
```

<span id="shrinkclause" name="shrinkclause" class="yaslink"></span>

### 5. shrink\_clause

该语句用于根据各个数据文件的空间使用情况回收空闲空间，收缩表空间集大小。

#### 5.1. keep size\_clause

将表空间集收缩至指定大小，如指定保留的值超过表空间集大小，则收缩失败并返回错误。

不指定KEEP时，系统将最大化收缩表空间集。

示例（存算一体分布式集群部署）

```sql
ALTER TABLESPACE SET users SHRINK SPACE KEEP 100M;
 
ALTER TABLESPACE SET users SHRINK SPACE;
```

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

### 6. databucket\_clause

该语句用于修改表空间集中的databucket（数据桶）信息。

<span id="adddatabucketclause" name="adddatabucketclause" class="yaslink"></span>

#### 6.1. add\_databucket\_clause

该语句用于增加databucket，可同时挂载多个，以`,`分隔。对于新建databucket的描述与约束与CREATE TABLESPACE SET中[databucket_clause](./CREATE TABLESPACE SET.html#databucketclause)子句相同。

<span id="bucketclause" name="bucketclause" class="yaslink"></span>

##### 6.1.1. bucket\_clause

同CREATE TABLESPACE SET中[bucket_clause](./CREATE TABLESPACE SET.html#bucketclause)子句描述。

<span id="s3bucketclause" name="s3bucketclause" class="yaslink"></span>

###### 6.1.1.1. s3\_bucket\_clause

同CREATE TABLESPACE SET中[s3_bucket_clause](./CREATE TABLESPACE SET.html#s3bucketclause)子句描述。

示例（存算一体分布式集群部署）

```sql
ALTER TABLESPACE SET tbs_tb ADD DATABUCKET '?/local_fs/lscfile3' MAXSIZE 1G;
```

<span id="alterdatabucketclause" name="alterdatabucketclause" class="yaslink"></span>

#### 6.2. alter\_databucket\_clause

该语句用于修改databucket的读写属性，创建的databucket均默认为可读写属性。

##### 6.2.1. bucket\_name

同CREATE TABLESPACE SET中[bucket_clause](./CREATE TABLESPACE SET.html#bucketclause)子句中bucket_name相关描述。

##### 6.2.2. readonly|readwrite

readonly表示只读，readwrite表示可读写。

注意databucket修改为只读后不支持任何形式的写入操作，但用户执行的DML语句（非Bulkload操作）不受影响，执行Bulkload操作时由于需要生成SCOL数据，若表空间下没有可写入的databucket则会报错。

示例（存算一体分布式集群部署）

```sql
ALTER TABLESPACE SET tbs_tb ALTER DATABUCKET '?/local_fs/lscfile3' READONLY;
```

<span id="dropdatabucketclause" name="dropdatabucketclause" class="yaslink"></span>

#### 6.3. drop\_databucket\_clause

该语句用于删除databucket，每次只允许指定一个databucket删除。

##### 6.3.1. bucket\_name

同[alter_databucket_clause](#alterdatabucketclause)子句描述。

示例（存算一体分布式集群部署）

```sql
ALTER TABLESPACE SET tbs_tb DROP DATABUCKET '?/local_fs/lscfile3';
```
