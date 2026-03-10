通用描述
----

ALTER TABLESPACE语句用于更改一个已存在的表空间的相关属性。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**alter tablespace::=**

```ebnf+diagram
syntax::= ALTER TABLESPACE tablespace_name (datafile_clause|
databucket_clause|
shrink_clause|
offline_clause|
ONLINE|
rename_clause)
```

**[datafile\_clause](#datafiletempfileclauses)::=**

```ebnf+diagram
syntax::= ADD (DATAFILE|TEMPFILE) [(file_specification) {"," (file_specification)}]
| DROP (DATAFILE|TEMPFILE) file_name
```

**[file\_specification](#filespecification)::=**

```ebnf+diagram
syntax::= "'file_name'" SIZE size_clause [AUTOEXTEND (OFF|(ON [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]))] [PARALLEL parallel]
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
syntax::= "'bucket_name'" [s3_bucket_clause] MAXSIZE size_clause
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

**[shrink_clause](#shrinkclause)::=**

```ebnf+diagram
syntax::= SHRINK SPACE [KEEP size_clause]
```

**[offline\_clause](#offlineclause)::=**

```ebnf+diagram
syntax::= OFFLINE [NORMAL|TEMPORARY|IMMEDIATE]
```

**[rename\_clause](#renameclause)::=**

```ebnf+diagram
syntax::= RENAME TO space_name
```

<span id="datafiletempfileclauses" name="datafiletempfileclauses" class="yaslink"></span>

### 1. datafile\_clause

该语句用于修改表空间的数据文件。

存算一体分布式集群部署中，仅允许对通过CREATE TABLESPACE语句创建的表空间新增/删除数据文件，若修改表空间数据文件时出现节点故障，恢复措施见[用户表空间管理](../../全部手册/数据库管理/存储管理/逻辑空间管理/表空间管理/用户表空间管理)章节描述。

#### 1.1. add (datafile|tempfile)

为表空间增加一个或多个数据文件（临时文件需使用TEMPFILE关键字）。

在共享集群/分布式集群部署中，若本地临时表空间或本地SWAP表空间采用本地磁盘路径，添加一个文件等于添加一组文件，每一组文件数量的个数等于集群实例个数。

<span id="filespecification" name="filespecification" class="yaslink"></span>

file_specification的描述请参考[CREATE TABLESPACE](./CREATE TABLESPACE)。

当不指定file_specification时，系统按如下规则自动创建一个数据文件：

- 文件名称由表空间名称以及数据文件在表空间内的序号组合生成，如：tablespace_name1，tablespace_name2...，且统一转换为大写。
- 文件的默认大小为8192个BLOCK，文件路径为系统默认的数据文件路径。
- 对于非MEMORY MAPPED表空间，默认文件开启自动扩展，next为8192个块，maxsize为64MB个块。
- 如果没有显式的规定extent分配方式，extent的默认分配方式为系统自动分配。

#### 1.2. drop (datafile|tempfile)

删除表空间的某个数据文件（临时文件需使用TEMPFILE关键字）。

在共享集群/分布式集群部署中，若本地临时表空间或本地SWAP表空间采用本地磁盘路径，删除一个文件等于删除一组文件，每一组文件数量的个数等于集群实例个数。

删除表空间文件的规则如下：

- 不允许删除创建该表空间时指定的第一个数据文件以及非ONLINE的数据文件。
- 不允许删除UNDO表空间的数据文件。
- 不允许删除默认SWAP表空间的数据文件。
- 如需通过DROP TEMPFILE删除影子文件，需确保创建该临时文件的数据库为当前主库（若不是，需先切换为主库）并在该主库上执行DROP TEMPFILE语句，其他备库收到DROP TEMPFILE的redo后才会清理影子记录。

> **Note**: 
>
> MEMORY MAPPED表空间的数据文件删除后该文件内部ID以及全局ID在该表空间被删除并重启数据库之前都不允许被复用。
>
> 当增加数据文件导致文件的表空间内部ID到达表空间限制（63）时，删除已有文件也无法向该表空间添加数据文件。
>
> 如果删除数据文件中途数据库掉电，重启后磁盘可能会有残留文件，此时需要手动删除。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER TABLESPACE SYSTEM ADD DATAFILE 'sys1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G;
 
ALTER TABLESPACE yashan ADD DATAFILE 'yashan_add1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G;

ALTER TABLESPACE yashan ADD DATAFILE 'yashan_add2' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 4;

ALTER TABLESPACE yashan ADD DATAFILE;
 
ALTER TABLESPACE SYSTEM DROP DATAFILE 'sys1';
 
ALTER TABLESPACE yashan DROP DATAFILE 'yashan_add1';
```
<span id="databucketclause" name="databucketclause" class="yaslink"></span>

### 2. databucket\_clause

该语句用于修改表空间的databucket（数据桶）信息。

<span id="adddatabucketclause" name="adddatabucketclause" class="yaslink"></span>

#### 2.1. add\_databucket\_clause

为表空间增加databucket，可同时挂载多个，以`,`分隔。对于新建databucket的描述与约束与CREATE TABLESPACE中[databucket_clause](./CREATE TABLESPACE.html#databucketclause)子句相同。

<span id="bucketclause" name="bucketclause" class="yaslink"></span>

##### 2.1.1. bucket\_clause

同CREATE TABLESPACE中[databucket_clause](./CREATE TABLESPACE.html#databucketclause)子句描述。

<span id="s3bucketclause" name="s3bucketclause" class="yaslink"></span>

###### 2.1.1.1. s3\_bucket\_clause

同CREATE TABLESPACE中[s3_bucket_clause](./CREATE TABLESPACE.html#s3bucketclause)子句描述。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE lsc_tb ADD DATABUCKET '?/local_fs/lscfile_add3' MAXSIZE 1G;
```

<span id="alterdatabucketclause" name="alterdatabucketclause" class="yaslink"></span>

#### 2.2. alter\_databucket\_clause

该语句用于修改databucket的读写属性，创建的databucket均默认为可读写属性。

##### 2.2.1. bucket\_name

同CREATE TABLESPACE中[bucket_clause](./CREATE TABLESPACE.html#bucketclause)子句中bucket_name相关描述。

##### 2.2.2. readonly|readwrite

readonly表示只读，readwrite表示可读写。

注意databucket修改为只读后不支持任何形式的写入操作，但用户执行的DML语句（非Bulkload操作）不受影响，执行Bulkload操作时由于需要生成SCOL数据，若表空间下没有可写入的databucket则会报错。

示例（单机部署）

```sql
ALTER TABLESPACE lsc_tb ALTER DATABUCKET '?/local_fs/lscfile_add3' READONLY;
```

<span id="dropdatabucketclause" name="dropdatabucketclause" class="yaslink"></span>

#### 2.3. drop\_databucket\_clause

删除表空间的某个databucket，每次只允许指定一个databucket删除。

##### 2.3.1. bucket\_name

同[alter_databucket_clause](#alterdatabucketclause)子句描述。

示例（单机部署）

```sql
ALTER TABLESPACE lsc_tb DROP DATABUCKET '?/local_fs/lscfile_add3';
```

<span id="shrinkclause" name="shrinkclause" class="yaslink"></span>

### 3. shrink\_clause

该语句用于根据各个数据文件的空间使用情况回收空闲空间，收缩表空间大小。

共享集群/分布式集群部署中不允许执行shrink_clause。

> **Note**: 
>
>- 不能对UNDO表空间执行该语句。
>- 在主库上对SWAP和TEMP表空间进行shrink，不会同步到备库。

#### 3.1. keep size\_clause

将表空间收缩至指定大小，如表空间实际使用已超过该大小值，则收缩失败并返回错误。

不指定KEEP时，系统将最大化收缩表空间。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE SYSTEM SHRINK SPACE KEEP 100M;
 
ALTER TABLESPACE SYSTEM SHRINK SPACE;
```

<span id="offlineclause" name="offlineclause" class="yaslink"></span>

### 4. offline\_clause

改变表空间至脱机状态。本操作一般应用于如下业务场景：

- 数据库的部分数据不再开放访问，则可以将其所在表空间offline
- 需要对表空间里的数据文件进行重命名或重分配时，将表空间offline，则其下所有的数据文件均会被offline

当某个表空间被offline后，该表空间下的所有数据文件将不可读写也不可修改，且不能对该表空间执行DDL操作，包括增删数据文件等操作。

SYSTEM/SYSAUX/UNDO/SWAP/TEMPORARY等内置表空间不允许offline。

共享集群/分布式集群部署中不允许执行offline_clause。

> **Note**: 
>
> 被offline的表空间及其下数据文件仍为数据库的一部分，因此不能创建与它们同名的表空间或数据文件。

#### 4.1. normal| temporary | immediate

offline选项，可省略，默认为NORMAL。

OFFLINE NORMAL：当表空间所有的数据文件均没有脱机时，才会将表空间及数据文件offline，否则报错。

OFFLINE TEMPORARY：允许表空间中存在已经脱机的数据文件，只对online的数据文件执行offline。

OFFLINE IMMDIATE：对非脱机的数据文件立即执行offline，可能存在归档或redo日志没有完全应用于数据文件，处于不一致状态，所以该操作offline的数据文件无法直接online，需要使用yasrman工具对该表空间执行RECOVER一致性恢复后才可执行online，参考yasrman工具的recover命令[recover](../../全部手册/工具手册/yasrman/yasrman使用指导/RECOVER)。当数据库未开启归档模式时，不允许指定此选项执行offline。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE yashan OFFLINE;
 
ALTER TABLESPACE yashan1 OFFLINE NORMAL;

ALTER TABLESPACE yashan2 OFFLINE TEMPORARY;

ALTER TABLESPACE yashan3 OFFLINE IMMEDIATE;
```

### 5. online

改变脱机表空间至在线状态，使其正常可用。

影子状态的临时文件不存在物理文件，不能被online。

本操作将表空间及其下的所有数据文件online，如果存在无法online的数据文件，则执行失败并返回错误。

共享集群/分布式集群部署中不允许执行online。

> **Note**: 
>
> 主备部署模式下，表空间执行OFFLINE IMMEDIATE之后，必须在执行OFFLINE的实例上ONLINE该表空间，否则将导致备库数据异常或不可用。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE yashan ONLINE;
```

<span id="renameclause" name="renameclause" class="yaslink"></span>

### 6. rename\_clause

该语句用于表空间或数据文件重命名，限制如下：

- 不允许将某个表空间重命名为当前已存在的表空间名称。
- 不允许使用该语句对内置表空间进行重命名。
- 不允许使用该语句对OFFLINE的表空间进行重命名。
- 不允许使用该语句对正在使用的SWAP表空间进行重命名，如需对此进行重命名，先修改DEFAULT_SWAP_TABLESPACE配置参数，再进行重命名操作。
- 存算一体分布式集群部署中不允许对表空间或数据文件重命名。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER TABLESPACE yashan1 RENAME TO yashanDb;
```
