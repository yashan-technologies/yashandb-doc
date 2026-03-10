## 通用描述

CREATE DATABASE用于创建一个新的数据库，该语句只能在一个处于NOMOUNT状态的空实例（Instance）中执行。系统通过本语句成功创建新数据库后，同时将其启动至OPEN状态。

- 单机部署中，使用CREATE DATABASE语句创建一个新的单实例数据库，数据库文件路径仅支持指定为本地文件系统路径。

- 分布式部署中，通过yasboot安装过程中即在所有的分布式节点上依据配置文件中指定参数创建数据库，后续不再允许在某个节点上执行本语句（除非用于故障恢复），否则将导致分布式集群出现节点不一致情况。

- 共享集群部署中，必须使用CREATE CLUSTER DATABASE创建一个新的多实例数据库，且redo、undo、swap等所有数据文件须指定为[YFS](../../../共享集群/集群文件系统/00集群文件系统)路径，否则返回错误。

创建数据库的过程中会在指定或默认位置创建物理文件，如过程中出现任何异常导致创建数据库失败时，这些已创建的文件将残留，用户需进行手动清理，未清理且使用相同文件再次CREATE DATABASE时将失败。

## 语句定义

**create database::=**

```ebnf+diagram
syntax::= create_single_instance_database|create_multiple_instance_database
```

**[create_single_instance_database](#singleinstance)::=**

```ebnf+diagram
syntax::= CREATE DATABASE database_name [(attribute_clause) {" " (attribute_clause)}]
```

**[attribute\_clause](#attributeclause)::=**

```ebnf+diagram
syntax::=  (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  ((controlfile) {", " (controlfile)}) ")"| 
MAXDATAFILES integer| 
MAXDATABUCKETS integer| 
MAXINSTANCES integer| 
CHARACTER SET charset|
NATIONAL CHARACTER SET charset|
SET TIME_ZONE|
UNDO_SEGMENTS integer| 
MAXLOGFILES  integer| 
MAXLOGHISTORY integer|     
(ARCHIVELOG|NOARCHIVELOG)| 
double_write_file_clause|  
logfiles_clause|
tablespace_clause)  
{" " (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  ((controlfile) {", " (controlfile)}) ")"| 
MAXDATAFILES integer| 
MAXDATABUCKETS integer| 
MAXINSTANCES integer| 
CHARACTER SET charset|
NATIONAL CHARACTER SET charset|
SET TIME_ZONE|
UNDO_SEGMENTS integer| 
MAXLOGFILES  integer| 
MAXLOGHISTORY integer| 
(ARCHIVELOG|NOARCHIVELOG)| 
double_write_file_clause| 
logfiles_clause|
tablespace_clause)}
```

**[double\_write\_file\_clause](#doublewritefileclause)::=**
```ebnf+diagram
syntax::= DOUBLE_WRITE DATAFILE dbwfile SIZE size_clause
```

**[logfiles\_clause](#logfilesclause)::=**

```ebnf+diagram
syntax::= LOGFILE "(" (logfile SIZE size_clause [BLOCKSIZE size_clause]){"," (logfile SIZE size_clause [BLOCKSIZE size_clause])} ")"
```

**[tablespace\_clause](#tablespaceclause)::=**

```ebnf+diagram
syntax::= ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}
```

**[datafiles\_clause](#datafilesclause)::=**

```ebnf+diagram
syntax::= (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]) 
{"," (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)])} 
```

**[extent\_clause](#extentclause)::=**

```ebnf+diagram
syntax::= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause)
```

**[databucket\_clause](#databucketclause)::=**

```ebnf+diagram
syntax::= DATABUCKET (bucketurl) {"," (bucketurl)}
```

**[create_multiple_instance_database](#multipleinstance)::=**

```ebnf+diagram
syntax::= CREATE CLUSTER DATABASE database_name (INSTANCES integer | ([(attribute_clause) {" " (attribute_clause)}] (instance_clause) {" " (instance_clause)}) )
```

**[instance\_clause](#instanceclause)::=**

```ebnf+diagram
syntax::= INSTANCE "(" (logfiles_clause|UNDO TABLESPACE DATAFILE datafiles_clause)")"
```

<span id="singleinstance" name="singleinstance" class="yaslink"></span>

### 1. create\_single\_instance\_database

该语句用于创建一个单实例的数据库。

#### 1.1. database\_name

指定要创建的数据库的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="attributeclause" name="attributeclause" class="yaslink"></span>

#### 1.2. attribute\_clause

该语句用来指定创建数据库的配置。

##### 1.2.1. USER SYS IDENTIFIED BY

该语句用于指定SYS用户的口令，可省略，则数据库创建后SYS用户的口令仍为初始值，并可通过ALTER USER语句并更口令。

##### 1.2.2. CONTROLFILES

该语句用于指定创建数据库时，同时创建的控制文件，多个控制文件用`,`分隔，可省略，则系统根据CONTROL_FILES参数定义的默认值在磁盘上生成控制文件。YashanDB支持指定最多8个控制文件。

##### 1.2.3. MAXDATAFILES

该语句用于指定数据库能打开的数据文件的最大个数，该值决定了控制文件里Datafile Section所占的初始空间，可省略，则取系统默认值4096。YashanDB支持指定的最大值为16384。

##### 1.2.4. MAXDATABUCKETS

该语句用于指定数据库能挂载的databucket的最大个数，可省略，则取系统默认值256。YashanDB支持指定的最大值为4096。

<span id="maxinstances" name="maxinstances" class="yaslink"></span>

##### 1.2.5. MAXINSTANCES

该语句用于指定能同时挂载（Mount）该数据库的最大实例个数，可省略，则取系统默认值8。YashanDB支持指定的最大值为64。

##### 1.2.6. CHARACTER SET

该语句用于指定数据库存储数据到文件所使用的字符集，可省略，则取系统默认值UTF8。YashanDB支持指定的字符集有ASCII、GBK、UTF8、ISO8859-1、GB18030。

如需使用TAC表和LSC表，数据库的字符集必须设置为UTF8。

##### 1.2.7. NATIONAL CHARACTER SET 

该语句用于指定数据库使用的国家字符集，可省略，则取系统默认值UTF16。YashanDB支持指定的国家字符集仅有UTF16。

##### 1.2.8. SET TIME\_ZONE

该语句用于指定数据库使用时区，可省略，则取操作系统当前时区。YashanDB支持指定的范围为[-15:59~+15:59]。

##### 1.2.9. UNDO\_SEGMENTS

该语句用于指定数据库可自动扩展回滚段的最大个数，可省略，则为默认值64。YashanDB支持指定的最大值为1024。

##### 1.2.10. MAXLOGFILES

该语句用于指定redo日志文件的元数据预留个数，该值决定了控制文件里Redofile Section所占的初始空间，可省略，则取系统默认值256，YashanDB支持指定的最大值为4096。该值不限制实例可创建的redo日志文件个数，当创建的redo日志个数超过该值时，控制文件可能会扩展。

##### 1.2.11. MAXLOGHISTORY

该语句用于指定归档日志文件的元数据预留个数，该值决定了控制文件里Archfile Section所占的初始空间，可省略，则取系统默认值64000。YashanDB支持指定的最大值为1000000。该值不限制实例可创建的归档日志文件个数，当创建的归档日志个数超过该值时，控制文件可能会扩展。

##### 1.2.12. ARCHIVELOG|NOARCHIVELOG

该语句用于指定数据库是否开启归档模式，可省略，则取系统默认值NOARCHIVELOG。

<span id="doublewritefileclause" name="doublewritefileclause" class="yaslink"></span>

##### 1.2.13. double\_write\_file\_clause

该语句用于指定待新建的数据库双写文件的路径和大小，可省略，则在系统默认的数据文件路径下创建一个名称为dwf，大小为8192个BLOCK的文件。

DOUBLE_WRITE DATAFILE：标识双写文件的关键字。

dbwfile：双写文件的名称，未包含路径时取系统默认的数据文件路径。

SIZE：双写文件的大小，双写文件的可用最小值受配置参数DBWR_BUFFER_SIZE和DBWR_COUNT影响，最小值近似公式为`4 \* DBWR_BUFFER_SIZE \* DBWR_COUNT + 4M`，若指定的创建双写文件的大小过小会出现建库失败。

<span id="logfilesclause" name="logfilesclause" class="yaslink"></span>

##### 1.2.14. logfiles\_clause

该语句用于指定待新建的数据库redo日志文件的路径和大小，可省略，则在系统默认的数据文件路径下创建三个名称为redo1/redo2/redo3，大小为128M，BLOCKSIZE为4K的文件。

LOGFILE：标识redo日志文件的关键字。

logfile：日志文件的名称，未包含路径时取系统默认的数据文件路径。

SIZE：日志文件的大小。

BLOCKSIZE：日志文件的页面大小。默认是4K，最小512字节，最大32K。其值一般设置为操作系统BLOCK的大小，小于系统BLOCK大小的话，会有性能影响。一般不建议修改。

<span id="tablespaceclause" name="tablespaceclause" class="yaslink"></span>

##### 1.2.15. tablespace\_clause

创建数据库时会同时创建内置表空间，包括SYSTEM表空间、SYSAUX表空间、UNDO表空间、SWAP表空间、TEMP表空间（TEMPORARY）、USERS表空间（DEFAULT）以及USERS_AIM表空间（仅存在于分布式部署）。

该语句用于指定内置表空间的各项属性，可省略，省略则系统按下面各项给出的缺省值创建内置表空间。

###### 1.2.15.1. TEMPFILE|DATAFILE

表空间对应的数据文件类型，其中SWAP表空间和TEMP表空间的数据文件类型缺省为TEMPFILE，其他表空间缺省为DATAFILE。

<span id="datafilesclause" name="datafilesclause" class="yaslink"></span>

###### 1.2.15.2. datafiles\_clause

表空间对应的数据文件名称，多个数据文件用`,`分隔。缺省在系统默认的数据文件路径下创建名称与表空间名称相同（DEFAULT表空间对应的数据文件名默认为users），大小为8192个BLOCK，开启自动扩展，每次扩展8192个BLOCK，最大可扩展到64MB个BLOCK（UNDO表空间为8MB个BLOCK）的数据文件。

<span id="extentclause" name="extentclause" class="yaslink"></span>

###### 1.2.15.3. extent\_clause

表空间对象申请extent时的空间分配方式，AUTOALLOCATE分配方式根据对象当前大小由系统自动分配extent空间，UNIFORM分配方式每次为对象分配的extent空间为固定值。

- UNDO表空间、SWAP表空间不允许指定extent分配方式，只能使用UNIFORM分配方式，默认为UNIFORM SIZE 1个BLOCK。

- TEMP表空间若不指定分配方式，默认为UNIFORM SIZE 8个BLOCK。

- 其它内置表空间默认为AUTOALLOCATE（系统自动分配）。

###### 1.2.15.4. MEMORY MAPPED

指定是否启用数据常驻内存模式，默认不启用。

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

###### 1.2.15.5. databucket\_clause

当为表空间指定了DATABUCKET时，系统将按指定路径信息创建用于对象存储的目录。对于DEFAULT表空间，如未指定DATABUCKET，系统将在默认路径（local_fs）下创建users文件夹用于对象存储。

示例（单机部署）

```sql
CREATE DATABASE yashan
CHARACTER SET utf8
NATIONAL CHARACTER SET utf16
ARCHIVELOG
LOGFILE ('/home/yasdb/YASDB_DATA/dbfiles/redo1' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo2' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo3' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo4' size 2G BLOCKSIZE 512)
UNDO TABLESPACE DATAFILE '?/dbfiles/undo' size 2G autoextend ON NEXT 256M MAXSIZE 64G
SWAP TABLESPACE TEMPFILE '?/dbfiles/swap' size 2G autoextend ON NEXT 256M MAXSIZE 64G
SYSTEM TABLESPACE DATAFILE 'system' size 128M MEMORY MAPPED
DEFAULT TABLESPACE DATAFILE '?/dbfiles/users' size 5G;
```

<span id="multipleinstance" name="multipleinstance" class="yaslink"></span>

### 2. create\_multiple\_instance\_database

该语句用于在共享集群部署下创建多实例数据库。

#### 2.1. database\_name

指定要创建的数据库的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

#### 2.2. INSTANCES integer

该语句用于指定共享集群部署下多实例数据库的实例个数，最大值为4，可省略。若省略，则必须指定[instance_clause](#instanceclause)。

#### 2.3. attribute\_clause

指定除redo文件和undo文件之外的数据库配置，这些属性定义与创建单实例数据库时的属性定义一致，见create_single_instance_database中的attribute_clause。

> **Note**：
>
> 共享集群部署中redo、undo、swap、default等所有数据文件路径需指定为YFS路径，否则返回错误。

<span id="instanceclause" name="instanceclause" class="yaslink"></span>

#### 2.4. instance\_clause

该语句用于指定共享集群部署下多实例数据库每个实例相关undo、redo日志文件和数据文件配置，根据所需实例个数多次指定本语句，最多支持指定4次（即创建4个实例），语句之间使用空格间隔。

示例（共享集群部署）

```sql
--通过INSTANCES关键字创建2个实例的数据库，数据库文件均为默认属性
CREATE cluster DATABASE yashan instances 2; 
--通过instance_clause语句创建2个实例的数据库，且自定义数据库文件属性
CREATE cluster DATABASE yashan
SYSTEM TABLESPACE DATAFILE 'system' size 64M
SYSAUX TABLESPACE DATAFILE 'sysaux' size 64M
DEFAULT TABLESPACE DATAFILE 'users' size 64M
TEMPORARY TABLESPACE TEMPFILE 'temp' size 64M
SWAP TABLESPACE TEMPFILE 'swap' size 64M
instance(
LOGFILE('logfile11' size 32M, 'logfile12' size 32M, 'logfile13' size 32M)
UNDO TABLESPACE DATAFILE 'undo1' size 64M
)
instance (
LOGFILE('logfile21' size 32M, 'logfile22' size 32M, 'logfile23' size 32M)
UNDO TABLESPACE DATAFILE 'undo2' size 64M
); 
--指定路径的配置
CREATE cluster DATABASE yashan
instance (
LOGFILE('+DG_0/redo1' size 128M BLOCKSIZE 512,
'+DG_0/redo2' size 128M BLOCKSIZE 512)
UNDO TABLESPACE DATAFILE '+DG_0/undo1' size 128M autoextend ON)
SWAP TABLESPACE TEMPFILE '+DG_0/swap' size 128M autoextend ON
SYSAUX TABLESPACE DATAFILE '+DG_0/sysaux' size 128M autoextend ON
SYSTEM TABLESPACE DATAFILE '+DG_0/system' size 128M autoextend ON
TEMPORARY TABLESPACE TEMPFILE '+DG_0/temp' size 128M autoextend ON
DEFAULT TABLESPACE DATAFILE '+DG_0/users' size 128M autoextend ON;
```

