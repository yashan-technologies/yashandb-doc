通用描述
----

ALTER TABLESPACE语句用于更改一个已存在的表空间的相关属性。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**alter tablespace::=**

```ebnf
= ALTER TABLESPACE tablespace_name (datafile_clause|
databucket_clause|
shrink_clause|
offline_clause|
ONLINE|
rename_clause).
```

**[datafile\_clause](#datafiletempfileclauses)::=**

```ebnf
= ADD (DATAFILE|TEMPFILE) [file_specification {"," file_specification}]
| DROP (DATAFILE|TEMPFILE) file_name.
```

**[file\_specification](#filespecification)::=**

```ebnf
= "'file_name'" SIZE size_clause [AUTOEXTEND (OFF|(ON [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]))] [PARALLEL parallel].
```

**[databucket_clause](#databucketclause)::=**

```ebnf
= add_databucket_clause | alter_databucket_clause | drop_databucket_clause.
```

**[add_databucket_clause](#adddatabucketclause)::=**

```ebnf
= ADD DATABUCKET (bucket_clause {"," bucket_clause}).
```

**[bucket_clause](#bucketclause)::=**

```ebnf
= "'bucket_name'" [s3_bucket_clause] MAXSIZE size_clause.
```

**[s3_bucket_clause](#s3bucketclause)::=**

```ebnf
= S3 "(" URL "'url'" ["," REGION "'region'"] "," ACCESS KEY "'access_key'" "," SECRET KEY "'secret_key'" ")".
```

**[alter_databucket_clause](#alterdatabucketclause)::=**

```ebnf
= ALTER DATABUCKET "'bucket_name'" (READONLY | READWRITE).
```

**[drop_databucket_clause](#dropdatabucketclause)::=**

```ebnf
= DROP DATABUCKET "'bucket_name'".
```

**[shrink_clause](#shrinkclause)::=**

```ebnf
= SHRINK SPACE [KEEP size_clause].
```

**[offline\_clause](#offlineclause)::=**

```ebnf
= OFFLINE [NORMAL|TEMPORARY|IMMEDIATE].
```

**[rename\_clause](#renameclause)::=**

```ebnf
= RENAME TO space_name.
```

<span id="datafiletempfileclauses" name="datafiletempfileclauses"></span>

### datafile\_clause

该语句用于修改表空间的数据文件。

存算一体分布式集群部署中，仅允许对通过CREATE TABLESPACE语句创建的表空间新增/删除数据文件，若修改表空间数据文件时出现节点故障，恢复措施见[用户表空间管理](../../../数据库管理/存储管理/逻辑空间管理/表空间管理/用户表空间管理)章节描述。

#### add (datafile|tempfile)

为表空间增加一个或多个数据文件（临时文件需使用TEMPFILE关键字）。

在共享集群/分布式集群部署中，若本地临时表空间或本地SWAP表空间采用本地磁盘路径，添加一个文件等于添加一组文件，每一组文件数量的个数等于集群实例个数。

<span id="filespecification" name="filespecification"></span>

file\_specification的描述请参考[CREATE TABLESPACE](./CREATE TABLESPACE)。

当不指定file\_specification时，系统按如下规则自动创建一个数据文件：

- 文件名称由表空间名称以及数据文件在表空间内的序号组合生成，如：tablespace_name1，tablespace_name2...，且统一转换为大写。
- 文件的默认大小为8192个BLOCK，文件路径为系统默认的数据文件路径。
- 对于非MEMORY MAPPED表空间，默认文件开启自动扩展，next为8192个块，maxsize为64MB个块。
- 如果没有显式的规定extent分配方式，extent的默认分配方式为系统自动分配。

#### drop (datafile|tempfile)

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
ALTER TABLESPACE system ADD DATAFILE 'sys1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G;
 
ALTER TABLESPACE yashan ADD DATAFILE 'yashan_add1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G;

ALTER TABLESPACE yashan ADD DATAFILE 'yashan_add2' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 4;

ALTER TABLESPACE yashan ADD DATAFILE;
 
ALTER TABLESPACE system DROP DATAFILE 'sys1';
 
ALTER TABLESPACE yashan DROP DATAFILE 'yashan_add1';
```
<span id="databucketclause" name="databucketclause"></span>

### databucket_clause

该语句用于修改表空间的databucket（数据桶）信息。

<span id="adddatabucketclause" name="adddatabucketclause"></span>

#### add_databucket_clause

为表空间增加databucket，可同时挂载多个，以`,`分隔。对于新建databucket的描述与约束与CREATE TABLESPACE中[databucket_clause](./CREATE TABLESPACE.md#databucketclause)子句相同。

<span id="bucketclause" name="bucketclause"></span>

##### bucket_clause

同CREATE TABLESPACE中[databucket_clause](./CREATE TABLESPACE.md#databucketclause)子句描述。

<span id="s3bucketclause" name="s3bucketclause"></span>

###### s3_bucket_clause

同CREATE TABLESPACE中[s3_bucket_clause](./CREATE TABLESPACE.md#s3bucketclause)子句描述。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE lsc_tb ADD DATABUCKET '?/local_fs/lscfile_add3' MAXSIZE 1G;
```

<span id="alterdatabucketclause" name="alterdatabucketclause"></span>

#### alter_databucket_clause

该语句用于修改databucket的读写属性，创建的databucket均默认为可读写属性。

##### bucket_name

同CREATE TABLESPACE中[bucket_clause](./CREATE TABLESPACE.md#bucketclause)子句中bucket_name相关描述。

##### readonly|readwrite

readonly表示只读，readwrite表示可读写。

注意databucket修改为只读后不支持任何形式的写入操作，但用户执行的dml语句（非Bulkload操作）不受影响，执行Bulkload操作时由于需要生成SCOL数据，若表空间下没有可写入的databucket则会报错。

示例（单机部署）

```sql
ALTER TABLESPACE lsc_tb ALTER DATABUCKET '?/local_fs/lscfile_add3' READONLY;
```

<span id="dropdatabucketclause" name="dropdatabucketclause"></span>

#### drop_databucket_clause

删除表空间的某个databucket，每次只允许指定一个databucket删除。

##### bucket_name

同[alter_databucket_clause](#alterdatabucketclause)子句描述。

示例（单机部署）

```sql
ALTER TABLESPACE lsc_tb DROP DATABUCKET '?/local_fs/lscfile_add3';
```

<span id="shrinkclause" name="shrinkclause"></span>

### shrink_clause

该语句用于根据各个数据文件的空间使用情况回收空闲空间，收缩表空间大小。

共享集群/分布式集群部署和分布式内存数据库中不允许执行shrink_clause。

> **Note**: 
>
>- 不能对UNDO表空间执行该语句。
>- 在主库上对SWAP和TEMP表空间进行shrink，不会同步到备库。

#### keep size_clause

将表空间收缩至指定大小，如表空间实际使用已超过该大小值，则收缩失败并返回错误。

不指定KEEP时，系统将最大化收缩表空间。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE system SHRINK SPACE KEEP 100M;
 
ALTER TABLESPACE system SHRINK SPACE;
```

<span id="offlineclause" name="offlineclause"></span>

### offline_clause

将用户创建的表空间调整为脱机状态。表空间脱机后，该表空间下的所有数据文件将不可读写，且不能对该表空间执行DDL操作，包括增删数据文件等操作。

表空间脱机功能的使用规则如下：

- 不允许操作内置表空间，包括SYSTEM/SYSAUX/UNDO/SWAP/TEMPORARY等。

- 脱机的表空间及其数据文件仍为数据库的一部分，因此不能创建与之同名的表空间或数据文件。

<span id="offline_option" name="offline_option"></span>

#### NORMAL|TEMPORARY|IMMEDIATE

脱机选项，可省略，默认为NORMAL。

| 脱机选项| NORMAL| TEMPORARY| IMMEDIATE|
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 前提约束                     | 要求目标表空间所有的数据文件均处于联机状态，否则报错         | 无                                                           | 要求数据库已开启归档模式，否则报错                           |
| 操作对象                     | 表空间及其所有非影子状态的数据文件<br />此类操作始终跳过影子文件（V$DATAFILE视图的SHADOW字段值） | 表空间及其所有联机的非影子状态数据文件<br />此类操作始终跳过影子文件 | 同TEMPORARY                                                  |
| 操作机制                     | 等待数据刷盘后再脱机                                         | 同NORMAL                                                     | 立即脱机<br />**可能导致无法恢复，请谨慎选择**               |
| 联机恢复                     | 无需做任何恢复操作，直接可再联机                             | 同NORMAL                                                     | 取决于目标表空间的数据文件状态（V$DATAFILE视图的STATUS字段值）：<br />- 全为OFFLINE状态：同NORMAL<br />- 存在RECOVER状态：详见下文介绍 |

执行OFFLINE IMMEDIATE时，可能会因归档或redo日志暂未完全应用于目标数据文件而导致不一致（目标数据文件会被置于RECOVER状态），此类数据文件将无法直接再联机，**请谨慎操作**。

对于存在RECOVER状态数据文件的表空间，后续处理方案及**注意事项**如下：

- 单机部署的非临时表空间中的RECOVER状态数据文件：可以尝试通过yasrman的[RECOVER命令](../../../工具手册/yasrman/yasrman使用指导/RECOVER)进行表空间恢复，恢复成功后此类数据文件方可再次联机。RECOVER命令与再次联机操作都必须在原来执行脱机操作的同一节点上执行，**无法跨节点恢复**。

- 其他场景：无法直接恢复此类文件，若存在表空间脱机前的可用备份集，可尝试通过恢复数据库从而恢复表空间即数据文件至正常状态。
  
- 在主备高可用部署中，若在有RECOVER状态数据文件的情况下发生过主备切换，在新主库上执行目标表空间联机操作会导致RECOVER状态数据文件所在备库异常，需BUILD备库进行修复。

> **Warn**:
> 
> 以下场景中的表空间不支持[表空间恢复功能](../../../工具手册/yasrman/yasrman使用指导/RECOVER.md)，offline操作需谨慎，recover后该文件及其表空间只能删除，不能恢复至online状态：
> 
> - 共享集群/分布式集群部署中的所有表空间
> 
> - 其他部署形态中的临时表空间

示例

```sql
ALTER TABLESPACE yashan OFFLINE;
 
ALTER TABLESPACE yashan1 OFFLINE NORMAL;

ALTER TABLESPACE yashan2 OFFLINE TEMPORARY;

ALTER TABLESPACE yashan3 OFFLINE IMMEDIATE;
```

### online

将脱机的表空间调整为联机状态，使其正常可用。

表空间联机功能的使用规则如下：

- 该操作的操作对象为目标表空间及其所有数据文件（影子文件除外，该操作始终跳过影子文件），若存在无法联机的数据文件则整个操作执行失败并返回错误。

- 在主备高可用部署中，若备库上某个表空间存在RECOVER状态的数据文件，在主库执行该表空间的联机操作会导致备库异常，需BUILD备库进行修复。

示例

```sql
ALTER TABLESPACE yashan ONLINE;
```

<span id="renameclause" name="renameclause"></span>

### rename_clause

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
