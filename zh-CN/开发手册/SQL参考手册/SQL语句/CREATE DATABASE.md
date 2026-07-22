## 通用描述

CREATE DATABASE用于创建一个新的数据库，该语句只能在一个处于NOMOUNT状态的空实例（Instance）中执行。系统通过本语句成功创建新数据库后，同时将其启动至OPEN状态。

- 单机部署中，使用CREATE DATABASE语句创建一个新的单实例数据库，数据库文件路径仅支持指定为本地文件系统路径。

- 共享集群/分布式集群部署中，必须使用CREATE CLUSTER DATABASE创建一个新的多实例数据库，且redo、undo、swap等所有数据文件须指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径，否则返回错误。

- 存算一体分布式集群部署中，通过yasboot安装过程中即在所有的分布式节点上依据配置文件中指定参数创建数据库，后续不再允许在某个节点上执行本语句（除非用于故障恢复），否则将导致分布式集群出现节点不一致情况。

创建数据库的过程中会在指定或默认位置创建物理文件，如过程中出现任何异常导致创建数据库失败时，这些已创建的文件将残留，用户需进行手动清理，未清理且使用相同文件再次CREATE DATABASE时将失败。

## 语句定义

**create database::=**

```ebnf
= create_single_instance_database|create_multiple_instance_database.
```

**[create_single_instance_database](#singleinstance)::=**

```ebnf
= CREATE DATABASE database_name [attribute_clause {" " attribute_clause}].
```

**[attribute_clause](#attributeclause)::=**

```ebnf
=  (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  (controlfile {", " controlfile}) ")"| 
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
tablespace_clause|
enable_pluggable_database|
IN MEMORY)  
{" " (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  (controlfile {", " controlfile}) ")"| 
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
tablespace_clause|
enable_pluggable_database|
IN MEMORY)}.
```

**[double_write_file_clause](#doublewritefileclause)::=**

```ebnf
= DOUBLE_WRITE DATAFILE dbwfile SIZE size_clause.
```

**[logfiles_clause](#logfilesclause)::=**

```ebnf
= LOGFILE "(" (logfile SIZE size_clause [BLOCKSIZE size_clause]){"," (logfile SIZE size_clause [BLOCKSIZE size_clause])} ")".
```

**[tablespace_clause](#tablespaceclause)::=**

```ebnf
= ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}.
```

**[datafiles_clause](#datafilesclause)::=**

```ebnf
= (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]) 
{"," (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)])} .
```

**[extent_clause](#extentclause)::=**

```ebnf
= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause).
```

**[databucket_clause](#databucketclause)::=**

```ebnf
= DATABUCKET bucketurl {"," bucketurl}.
```

**[enable_pluggable_database](#enablepluggabledatabase)**

```ebnf
= ENABLE PLUGGABLE DATABASE [SEED 
((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}].
```

**[create_multiple_instance_database](#multipleinstance)::=**

```ebnf
= CREATE CLUSTER DATABASE database_name (INSTANCES integer | ([attribute_clause {" " attribute_clause}] instance_clause {" " instance_clause}) ).
```

**[instance_clause](#instanceclause)::=**

```ebnf
= INSTANCE "(" (logfiles_clause|UNDO TABLESPACE DATAFILE datafiles_clause)")".
```

<span id="singleinstance" name="singleinstance"></span>

### create_single_instance_database

该语句用于创建一个单实例的数据库。

#### database_name

指定要创建的数据库的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="attributeclause" name="attributeclause"></span>

#### attribute_clause

该语句用来指定创建数据库的配置。

##### USER SYS IDENTIFIED BY

该语句用于指定SYS用户的口令，可省略，则数据库创建后SYS用户的口令仍为初始值，并可通过ALTER USER语句并更口令。

##### CONTROLFILES

该语句用于指定创建数据库时，同时创建的控制文件，多个控制文件用`,`分隔，可省略，则系统根据CONTROL_FILES参数定义的默认值在磁盘上生成控制文件。YashanDB支持指定最多8个控制文件。

##### MAXDATAFILES

该语句用于指定数据库能打开的数据文件的最大个数，该值决定了控制文件里Datafile Section所占的初始空间，可省略，则取系统默认值4096。YashanDB支持指定的最大值为16384。

##### MAXDATABUCKETS

该语句用于指定数据库能挂载的databucket的最大个数，可省略，则取系统默认值256。YashanDB支持指定的最大值为4096。

<span id="maxinstances" name="maxinstances"></span>

##### MAXINSTANCES

该语句用于指定能同时挂载（Mount）该数据库的最大实例个数，可省略，则取系统默认值8。YashanDB支持指定的最大值为64。

该语句用于指定单机及分布式支持的最大实例数量，或共享集群数据库在线扩容的最大节点数量。单机及分布式部署时，23.4版本仅支持单库单实例，后续版本将规划单库多实例能力。

##### CHARACTER SET

该语句用于指定数据库存储数据到文件所使用的字符集，可省略，则取系统默认值UTF8。YashanDB支持指定的字符集有ASCII、GBK、UTF8、ISO8859-1、GB18030。

如需使用TAC表和LSC表，数据库的字符集必须设置为UTF8。

##### NATIONAL CHARACTER SET 

该语句用于指定数据库使用的国家字符集，可省略，则取系统默认值UTF16。YashanDB支持指定的国家字符集仅有UTF16。

##### SET TIME_ZONE

该语句用于指定数据库使用时区，可省略，则取操作系统当前时区。YashanDB支持指定的范围为[-15:59~+15:59]。

##### UNDO_SEGMENTS

该语句用于指定数据库可自动扩展回滚段的最大个数，可省略，则为默认值64。YashanDB支持指定的最大值为1024。

##### MAXLOGFILES

该语句用于指定redo日志文件的元数据预留个数，该值决定了控制文件里Redofile Section所占的初始空间，可省略，则取系统默认值256，YashanDB支持指定的最大值为4096。该值不限制实例可创建的redo日志文件个数，当创建的redo日志个数超过该值时，控制文件可能会扩展。

##### MAXLOGHISTORY

该语句用于指定归档日志文件的元数据预留个数，该值决定了控制文件里Archfile Section所占的初始空间，可省略，则取系统默认值64000。YashanDB支持指定的最大值为1000000。该值不限制实例可创建的归档日志文件个数，当创建的归档日志个数超过该值时，控制文件可能会扩展。

##### ARCHIVELOG|NOARCHIVELOG

该语句用于指定数据库是否开启归档模式，可省略，则取系统默认值NOARCHIVELOG。

<span id="doublewritefileclause" name="doublewritefileclause"></span>

##### double_write_file_clause

该语句用于指定待新建的数据库双写文件的路径和大小，可省略，则在系统默认的数据文件路径下创建一个名称为dwf（单机部署）或double_write（存算一体分布式集群部署），大小为8192个BLOCK的文件。

共享集群/分布式集群部署中，不存在双写文件。

DOUBLE_WRITE DATAFILE：标识双写文件的关键字。

dbwfile：双写文件的名称，未包含路径时取系统默认的数据文件路径。

SIZE：双写文件的大小，双写文件的可用最小值受配置参数DBWR_BUFFER_SIZE和DBWR_COUNT影响，最小值近似公式为`4 \* DBWR_BUFFER_SIZE \* DBWR_COUNT + 4M`，若指定的创建双写文件的大小过小会出现建库失败。

<span id="logfilesclause" name="logfilesclause"></span>

##### logfiles_clause

该语句用于指定待新建的数据库redo日志文件的路径和大小，可省略，则在系统默认的数据文件路径下创建三个名称为redo1/redo2/redo3，大小为128M，BLOCKSIZE为4K的文件。

LOGFILE：标识redo日志文件的关键字。

logfile：日志文件的名称，未包含路径时取系统默认的数据文件路径。

SIZE：日志文件的大小。

BLOCKSIZE：日志文件的页面大小，默认为4K，最小512字节，最大32K，通常设置为操作系统BLOCK的大小。若该值配置小于系统BLOCK，可能会影响性能。

<span id="tablespaceclause" name="tablespaceclause"></span>

##### tablespace_clause

创建数据库时会同时创建内置表空间，包括SYSTEM表空间、SYSAUX表空间、UNDO表空间、SWAP表空间、TEMP表空间（TEMPORARY）、USERS表空间（DEFAULT）以及USERS_AIM表空间（仅存在于存算一体分布式集群部署）。

该语句用于指定内置表空间的各项属性，可省略，省略则系统按下面各项给出的缺省值创建内置表空间。

###### TEMPFILE|DATAFILE

表空间对应的数据文件类型，其中SWAP表空间和TEMP表空间的数据文件类型缺省为TEMPFILE，其他表空间缺省为DATAFILE。

<span id="datafilesclause" name="datafilesclause"></span>

###### datafiles_clause

表空间对应的数据文件名称，多个数据文件用`,`分隔。缺省在系统默认的数据文件路径下创建名称与表空间名称相同（DEFAULT表空间对应的数据文件名默认为users），大小为8192个BLOCK，开启自动扩展，每次扩展8192个BLOCK，最大可扩展到64MB个BLOCK（UNDO表空间为8MB个BLOCK）的数据文件。

<span id="extentclause" name="extentclause"></span>

###### extent_clause

表空间对象申请extent时的空间分配方式，AUTOALLOCATE分配方式根据对象当前大小由系统自动分配extent空间，UNIFORM分配方式每次为对象分配的extent空间为固定值。

- UNDO表空间、SWAP表空间不允许指定extent分配方式，只能使用UNIFORM分配方式，默认为UNIFORM SIZE 1个BLOCK。

- TEMP表空间若不指定分配方式，默认为UNIFORM SIZE 8个BLOCK。

- 其它内置表空间默认为AUTOALLOCATE（系统自动分配）。

###### MEMORY MAPPED

指定是否启用数据常驻内存模式，默认不启用。

<span id="databucketclause" name="databucketclause"></span>

###### databucket_clause

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
UNDO TABLESPACE DATAFILE '?/dbfiles/undo' size 2G autoextend on next 256M maxsize 64G
SWAP TABLESPACE TEMPFILE '?/dbfiles/swap' size 2G autoextend on next 256M maxsize 64G
SYSTEM TABLESPACE DATAFILE 'system' size 128M MEMORY MAPPED
DEFAULT TABLESPACE DATAFILE '?/dbfiles/users' size 5G;
```

<span id="enablepluggabledatabase" name="enablepluggabledatabase"></span>

##### enable_pluggable_database

该语句用于创建容器数据库CDB，每个CDB创建后默认包含两个内置的容器：


- 根容器（CDB root）：当前实例将转换为CDB Root，默认名称为`CDB$ROOT`。根容器本身是一个功能完整的数据库实例，拥有独立的数据文件和事务系统，但其核心职责是管理整个CDB的元数据、全局用户、资源计划及PDB生命周期。所有的容器管理操作（例如创建、删除PDB）必须在根容器中执行。  

- 种子容器（PDB seed）：系统会自动创建一个名称为PDB$SEED的只读PDB，作为创建PDB的标准模板，默认处于关闭状态。其配置由SEED子句定义，未定义或无法定义的部分配置会默认继承至根容器，例如字符集。后续创建PDB（CREATE PLUGGABLE DATABASE）时均会通过克隆PDB$SEED生成，包括系统表空间结构、初始用户、权限配置等。  


创建容器数据库需遵循以下规则：  

- 仅适用于单机（主备）部署、共享集群部署（不能为主备集群部署）或分布式集群部署。  

- 需确保容器数据库功能已开启（即配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），默认为FALSE。  

- enable_pluggable_database子句与其他子句/关键字属性，无强制顺序关系。

- 如需定义种子容器的属性，应在SEED关键字后连续定义完种子容器的属性。尤其是内置表空间（SYSTEM、SYSAUX、UNDO、SWAP或TEMPORARY），若同时指定种子容器和根容器的某个内置表空间属性（即语句中出现**2次**同名内置表空间属性子句），SEED关键字后第2次出现的内置表空间属性及其后所有表空间定义都将视作根容器的属性。

###### SEED tablespace_clause

创建种子容器时会同时创建内置表空间，包括SYSTEM表空间、SYSAUX表空间、UNDO表空间、SWAP表空间、TEMP表空间（TEMPORARY）。  

该语句用于指定种子容器内置表空间的各项属性，语法规则同[tablespace_clause](#tablespaceclause)。  

示例（单机部署）

```sql
-- 不指定种子容器属性
CREATE DATABASE yashancdb ARCHIVELOG ENABLE PLUGGABLE DATABASE;

-- 指定种子容器部分属性
CREATE DATABASE yashancdb
	SYSTEM TABLESPACE DATAFILE 'system' size 500M
	SYSAUX TABLESPACE DATAFILE 'sysaux' size 500M
	DEFAULT TABLESPACE DATAFILE 'usersroot' size 500M
	ARCHIVELOG
	ENABLE PLUGGABLE DATABASE
		SEED
			SYSTEM TABLESPACE DATAFILE 'system' size 128M
			SYSAUX TABLESPACE DATAFILE 'sysaux' size 128M;
```

##### IN MEMORY

崖山数据库已支持分布式内存数据库开放试用，支持在安装阶段指定部署为分布式内存数据库形态，不允许通过该语法创建分布式内存数据库实例。该语法仅作为创建单机内存数据库实例的语法支持，不具有实际作用。

<span id="multipleinstance" name="multipleinstance"></span>

### create_multiple_instance_database

该语句用于在共享集群/分布式集群部署下创建多实例数据库。

#### database_name

指定要创建的数据库的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

#### INSTANCES integer

该语句用于指定共享集群/分布式集群部署下多实例数据库的实例个数，最大值为4，可省略。若省略，则必须指定[instance_clause](#instanceclause)。

#### attribute_clause

指定除redo文件和undo文件之外的数据库配置，这些属性定义与创建单实例数据库时的属性定义一致，详情请查阅create_single_instance_database中的[attribute_clause](#attributeclause)。

> **Note**: 
>
> 共享集群/分布式集群部署中redo、undo、swap、default等所有数据文件路径需指定为YFS路径，否则返回错误。

<span id="instanceclause" name="instanceclause"></span>

#### instance_clause

该语句用于指定共享集群/分布式集群部署下多实例数据库每个实例相关undo、redo日志文件和数据文件配置，根据所需实例个数多次指定本语句，最多支持指定4次（即创建4个实例），语句之间使用空格间隔。

如需在共享集群/分布式集群部署下创建容器数据库，根容器和种子容器所指定的instance子句数量均须与实际的实例数量一致。

示例（共享集群/分布式集群部署）

```sql
-- 通过INSTANCES关键字创建2个实例的数据库，数据库文件均为默认属性
CREATE CLUSTER DATABASE yashan instances 2; 
-- 通过instance_clause语句创建2个实例的数据库，且自定义数据库文件属性
CREATE CLUSTER DATABASE yashan
system tablespace datafile 'system' size 64M
sysaux tablespace datafile 'sysaux' size 64M
default tablespace datafile 'users' size 64M
temporary tablespace tempfile 'temp' size 64M
swap tablespace tempfile 'swap' size 64M
instance(
logfile('logfile11' size 32M, 'logfile12' size 32M, 'logfile13' size 32M)
undo tablespace datafile 'undo1' size 64M
)
instance (
logfile('logfile21' size 32M, 'logfile22' size 32M, 'logfile23' size 32M)
undo tablespace datafile 'undo2' size 64M
); 

-- 通过INSTANCES关键字创建2个实例的容器数据库，其他均使用默认属性
CREATE CLUSTER DATABASE yashancdb INSTANCES 2 ENABLE PLUGGABLE DATABASE;

-- 通过instance_clause语句创建2个实例的容器数据库，且自定义数据库文件属性
create cluster database yashancdb2         
	instance (
        logfile('+DG0/dbfiles/redo1' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo2' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo3' size 256M BLOCKSIZE 512)
        UNDO TABLESPACE DATAFILE '+DG0/dbfiles/undo1' size 128M autoextend on
    )
    instance (
        logfile('+DG0/dbfiles/redo4' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo5' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo6' size 256M BLOCKSIZE 512)
        UNDO TABLESPACE DATAFILE '+DG0/dbfiles/undo2' size 128M autoextend on
    )
    SYSTEM TABLESPACE DATAFILE '+DG0/dbfiles/system' size 128M autoextend on
    SWAP TABLESPACE TEMPFILE '+DG0/dbfiles/swap' size 128M autoextend on
    DEFAULT TABLESPACE DATAFILE '+DG0/dbfiles/users' size 128M autoextend on    
    SYSAUX TABLESPACE DATAFILE '+DG0/dbfiles/sysaux' size 128M autoextend on
    TEMPORARY TABLESPACE TEMPFILE '+DG0/dbfiles/temp' size 128M autoextend on
    ARCHIVELOG
    ENABLE PLUGGABLE DATABASE
        SEED 
            SYSTEM TABLESPACE DATAFILE 'system' size 128M
            SYSAUX TABLESPACE DATAFILE 'sysaux' size 128M    
            SWAP TABLESPACE TEMPFILE 'swap' size 128M
            TEMPORARY TABLESPACE TEMPFILE 'temp' size 128M   
            instance (
                UNDO TABLESPACE DATAFILE '+DG0/containers/PDB$SEED/dbfiles/undo1' size 128M autoextend on
                logfile('+DG0/containers/PDB$SEED/dbfiles/redo1' size 128M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo2' size 256M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo3' size 512M BLOCKSIZE 512
                )
            )
            instance (
                UNDO TABLESPACE DATAFILE '+DG0/containers/PDB$SEED/dbfiles/undo2' size 128M autoextend on
                logfile('+DG0/containers/PDB$SEED/dbfiles/redo4' size 128M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo5' size 256M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo6' size 512M BLOCKSIZE 512
                )
            )        
    ;
```
