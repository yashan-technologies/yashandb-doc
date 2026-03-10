通用描述
----

ALTER SYSTEM用于动态地改变所在数据库实例（Instance）的属性，立即生效（但对于ALTER SYSTEM SET PARAMETER可设定为重启生效）且对所有用户生效。

语句定义
----

**alter system::=**

```ebnf+diagram
syntax::= ALTER SYSTEM (set_parameter_clause
|SWITCH LOGFILE
|CHECKPOINT
|FLUSH BUFFER_CACHE
|FLUSH SHARED_POOL
|EXTEND BUFFER_CACHE SIZE size_clause
|ARCHIVE LOG CURRENT
|kill_session_clause
|cancel_sql_clause
|IGNORE STANDBY MISMATCHED REDO
|dump_clause
|FLUSH GTS
|CLEAN RESIDUAL TABLESPACE
|CLEAN MESSAGE POOL)
```

**[set\_parameter\_clause](#setparameterclause)::=**

```ebnf+diagram
syntax::= SET parameter_name "=" parameter_value 
[CONTAINER "=" (CURRENT|ALL|pdb_name)]
[SCOPE "=" (SPFILE|MEMORY|BOTH)] 
[(TYPE "=" (CN|DN|MN|ALL)) | (NODE "=" node_id)]
```

**[kill\_session\_clause](#killsessionclause)::=**

```ebnf+diagram
syntax::= KILL SESSION "'" session_id "," session_serial "'"
```

**[cancel\_sql\_clause](#cancelsqlclause)::=**

```ebnf+diagram
syntax::= CANCEL SQL "'" session_id "," session_serial ["," sql_id] "'"
```

**[dump\_clause](#dumpclause)::=**

```ebnf+diagram
syntax::= DUMP (PRIVATE LOG
|LOGFILE file_name
|DATAFILE file_id [BLOCK block_id|MINBLOCK block_id MAXBLOCK block_id]
|SESSION sid BACKTRACE
|REDO TYPE)
```

<span id="setparameterclause" name="setparameterclause" class="yaslink"></span>

### 1. set\_parameter\_clause

该语句用于修改数据库的[配置参数](../../../参考手册/配置参数)。

ALTER SYSTEM SET修改配置参数，会打印修改信息的描述到RUN LOG中。

存算一体分布式集群部署中，仍更建议使用YCM图形化界面或者[yasboot](../../../工具手册/yasboot/yasboot命令介绍/yasboot config)命令行方式来修改配置参数。

如果parameter_name为YashanDB规定的只读参数，执行此语句时将会提示YAS-02138错误。

YashanDB规定了如下几类配置参数：

*   只读参数，不可被修改。
*   允许被修改，且修改后可以立即生效的配置参数。
*   允许被修改，但修改后需要重启实例才会生效的配置参数。

> **Note**: 
>
> 在分布式集群中，执行本语句默认修改当前节点。

#### 1.1. CONTAINER

该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），用于指定当前操作的容器作用域，可指定全局生效或当前容器本地生效，可省略。



在容器数据库中，配置参数根据其生效属性可分为：

- CDB全局参数：整个CDB级别的配置，仅允许连接根容器进行修改，仅对根容器生效。

- CDB私有参数：每个容器（根容器、PDB）独立的参数，只能直连目标容器进行自定义配置，仅对自身生效。

- 普通参数：每个容器可自定义配置也可直接继承根容器的参数（PDB中对应参数的旧值与根容器旧值一致表示属于继承关系），其生效范围取决于执行实例和指定的container属性。


CONTAINER属性可选值如下：

- CURRENT：默认值，表示为当前连接的数据库配置相应参数，即本地配置。若在连接根容器时指定为CURRENT仍可能为全局配置，其生效规则为先修改根容器的参数值，再下发给该CDB中的所有PDB继承。

- ALL：连接根容器操作时表示强制修改根容器及其所有PDB的参数，恢复PDB与根容器的继承关系。连接PDB操作时无特殊含义，效果等同于CURRENT。

- 某个PDB的名称：表示为该PDB配置相应参数，效果等同于直连该PDB进行本地配置。若连接PDB操作则只能指定为自己的名称。

示例（单机/共享集群/分布式集群部署）

```sql
-- CDB全局参数，必须连接根容器进行修改
SHOW PARAMETER MAX_PDBS;
NAME                  VALUE     
--------------------- -------   
MAX_PDBS              256      
  
ALTER SYSTEM SET MAX_PDBS=128 scope=BOTH;

SHOW PARAMETER MAX_PDBS;
NAME                  VALUE     
--------------------- -------   
MAX_PDBS              128     

-- 普通参数，假设连接根容器进行全局修改
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    300     
 
ALTER SYSTEM SET checkpoint_timeout=400 CONTAINER=ALL;

-- 根容器和具有继承关系的PDB均会生效
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    400

SELECT con_id,name,value FROM Containers(V$PARAMETER) WHERE name ='CHECKPOINT_TIMEOUT';

      CON_ID NAME                                                             VALUE
------------ ---------------------------------------------------------------- ----------------------------------------------------------------
           0 CHECKPOINT_TIMEOUT                                               400
           2 CHECKPOINT_TIMEOUT                                               400
```

#### 1.2. scope

SCOPE用于设定对配置参数修改后的生效方式，默认为BOTH。

*   SPFILE：将参数值写入磁盘，需重启才能生效
*   MEMORY：将参数值写入内存，立即生效，但重启后失效
*   BOTH：将参数值同时写入内存和磁盘，立即生效，重启后也生效

如果VALUE被赋予为YashanDB规定的不可立即生效参数，则必须同时指定SCOPE为SPFILE值，否则将会提示YAS-06001错误。

示例

```sql
SHOW PARAMETER data_buffer_size;
NAME                  VALUE     
--------------------- -------   
DATA_BUFFER_SIZE      64M       
  
ALTER SYSTEM SET data_buffer_size=128M scope=SPFILE;
SHOW PARAMETER data_buffer_size;
NAME                  VALUE     
--------------------- -------   
DATA_BUFFER_SIZE      64M       
  
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    300     
 
  
ALTER SYSTEM SET checkpoint_timeout=400;
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    400
```

#### 1.3. type

本语句只在存算一体分布式集群部署中使用，用于指定节点类型进行配置。

* CN：修改所有CN节点配置参数
* DN：修改所有DN节点配置参数
* MN：修改所有MN节点配置参数
* ALL：修改所有节点配置参数，包括所有CN、DN和MN

如果不指定TYPE，默认修改本节点配置参数。

> **Note**: 
>
> - 不允许在NOMOUNT/MOUNT模式下指定TYPE，默认修改本节点配置参数。
>
> - 对于未处于OPEN状态的节点，即使处于TYPE指定范围内，也无法跨节点修改其配置参数。
>
> - 上述描述的是连接到某一CN节点执行时的规则，如直连到其他节点，则不允许指定TYPE，默认修改本节点配置参数。

示例（存算一体分布式集群部署）

```sql
--修改所有CN节点上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = CN;

--修改所有DN节点上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = DN;

--修改所有MN节点上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = MN;

--修改所有CN、DN、MN节点上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = ALL;
```

#### 1.4. node

本语句只在存算一体分布式集群部署中使用，用于指定节点进行配置，node_id = g-n。
* g：节点所在组号ID
* n：节点ID，'*'表示组内所有节点

如果不指定NODE，默认修改本节点配置参数。

> **Note**: 
>
> 1.不允许在NOMOUNT/MOUNT模式下指定NODE，默认修改本节点配置参数。
>
> 2.对于未处于OPEN状态的节点，即使处于NODE指定范围内，也无法跨节点修改其配置参数。
>
> 3.上述描述的是连接到某一CN节点执行时的规则，如直连到其他节点，则不允许指定NODE，默认修改本节点配置参数。

示例（存算一体分布式集群部署）

```sql
--修改MN节点1-1上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH NODE = 1-1;

--修改所有CN节点上的RUN_LOG_FILE_COUNT
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH NODE = 2-*;
```

<span id="switch_logfile" name="switch_logfile" class="yaslink"></span>

### 2. switch logfile

该语句用于对数据库触发一次当前redo日志文件的强制切换，该操作不是进行归档的触发条件。

示例

```sql
ALTER SYSTEM SWITCH LOGFILE;
```

### 3. checkpoint

该语句用于对数据库触发一次全量的CHECKPOINT，以使内存中的脏数据写入到磁盘中。 

示例

```sql
ALTER SYSTEM CHECKPOINT;
```

### 4. flush buffer\_cache

该语句用于对数据库触发一次失效Buffer Cache中所有的Blocks，此操作会导致所有缓存数据被清除，其后的SQL查询将执行物理读取磁盘数据。

示例

```sql
ALTER SYSTEM FLUSH BUFFER_CACHE;
```

### 5. flush shared\_pool

该语句用于对数据库触发一次回收失效的pool内存资源，此操作会导致长期未使用的SQL语句缓存被清空，其后的相同的SQL语句使用将重新进行编译解析。

示例

```sql
ALTER SYSTEM FLUSH SHARED_POOL;
```

### 6. extend buffer\_cache size

该语句用于临时扩展数据库的Data Buffer大小，重启后失效。

#### 6.1. size\_clause

对Data Buffer指定一个整型值，单位可以为B/K/M/G/T/P/E。每次运行该语句所设定SIZE的限额受如下两个值制约：

*   所在服务器的物理内存
*   2^24\*DB_BLOCK_SIZE\*DATA_BUFFER_PARTS

其中，DATA_BUFFER_PARTS表示Data Buffer的分区数，系统默认为1，如需调整该值，请联系我们的技术支持处理。

超过限定值将会提示YAS-00101错误。

示例

```sql
ALTER SYSTEM EXTEND BUFFER_CACHE SIZE 4G;
```

<span id="archive_logfile" name="archive_logfile" class="yaslink"></span>

### 7. archive log current

该语句用于对数据库触发一次当前redo日志文件的切换和归档。此操作需要数据库的归档模式需处于开启状态，否则将提示YAS-02079错误。

示例

```sql
ALTER SYSTEM ARCHIVE LOG CURRENT;
```

<span id="killsessionclause" name="killsessionclause" class="yaslink"></span>

### 8. kill\_session\_clause

该语句用于终止一个指定的会话，并回滚（Rollback）此会话中未提交事务，释放此会话产生的锁（Lock），之后在此会话中运行SQL语句将提示连接错误信息。

执行此命令需提供会话的SID和SERIAL#。

示例（单机/共享集群/分布式集群部署）

```sql
-- 在sales用户下执行如下语句
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01' FOR UPDATE;
  
-- 查看锁及会话信息
SELECT SID,ID1,ID2,LMODE,REQUEST FROM V$LOCK;
    SID             ID1        ID2 LMODE     REQUEST   
-------- --------------- ---------- --------- ---------
      21            1328            TS                 
      21     21474844928          0 ROW                
  
SELECT sid,serial# FROM V$SESSION WHERE sid=21;
     SID      SERIAL#
-------- ------------
      21            2
          
  
-- 以下操作需要拥有管理员权限
-- 通过SID和SERIAL#参数终止指定会话
ALTER SYSTEM KILL SESSION '21,2';
```

**分布式跨CN会话终止**

在配置了多CN的存算一体分布式集群部署形态中，可使用本语句跨CN终止指定的客户端连接会话，且仅支持通过GLOBAL_SESSION_ID指定相应会话。

GLOBAL_SESSION_ID（即存算一体分布式集群部署中的全局会话ID）可通过USERENV函数或查询GV$SESSION视图获取。在具有相同GLOBAL_SESSION_ID的会话集中有且仅有1个会话连接客户端与服务端CN节点，其他则为服务端各节点间的内部会话（其生存周期与客户端连接会话同步），关于会话的详细内容可参考[会话管理](../../../数据库管理/运行监控/会话管理)。

示例（在配置了2-1和2-2两个CN节点的存算一体分布式集群部署形态中）

```sql
--连接2-1，建立一个会话，查看此会话的全局会话ID
--使用USERENV函数查询时使用GSID参数
SELECT USERENV('GSID') FROM dual;
USERENV('GSID') 
-------------- 
        131091

--获取连接到指定CN节点的会话标识，CN节点以group_id和group_node_id为唯一标识
SELECT global_session_id, serial# 
FROM GV$SESSION WHERE global_session_id IN (131091) AND group_id=2 AND group_node_id=1;
GLOBAL_SESSION_ID      SERIAL# 
----------------- ------------ 
           131091            5
           
--连接2-2，不要关闭2-1的会话，在2-2上终止2-1的会话
ALTER SYSTEM KILL SESSION '131091,5';
```

<span id="cancelsqlclause" name="cancelsqlclause" class="yaslink"></span>

### 9. cancel\_sql\_clause

该语句用于终止一个正在运行中的SQL操作，执行此命令需要提供该SQL操作所在会话的SID和SERIAL#。

指定SQL_ID时表示终止该SQL_ID对应的SQL操作，不指定时则表示终止该会话下当前正在运行的SQL操作。

在分布式下，支持跨CN终止SQL操作，需提供会话的GSID和SERIAL#。 

示例（单机/共享集群/分布式集群部署）：会话1——在sales用户下执行如下语句：

```sql
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01' FOR UPDATE;
```
示例（单机/共享集群/分布式集群部署）：会话2——在sales用户下执行如下语句：

```sql
--开启新会话2，以sales用户登录，添加等锁SQL，使其处于执行状态中
UPDATE area SET DHQ='Shanghai3' WHERE area_no='01';
```

示例（单机/共享集群/分布式集群部署）：会话3——在DBA权限用户下执行如下语句：

```sql
--开启新会话3，以DBA权限用户登录
SELECT SID,ID1,ID2,LMODE,REQUEST FROM V$LOCK WHERE request='ROW';
     SID            ID1       ID2 LMODE     REQUEST 
-------- -------------- --------- --------- ---------
      21     4447535124                     ROW     
  
--查询SQL_ID
SELECT sid,serial#,sql_id FROM V$SESSION WHERE sid=21;
    SID      SERIAL#                SQL_ID
-------- ------------ ---------------------
      21            1             3829447373       
  
--终止指定的SQL
ALTER SYSTEM CANCEL SQL '21,1,3829447373';

--终止正在运行的SQL
ALTER SYSTEM CANCEL SQL '21,1';
```

在存算一体分布式集群部署形态中，上述会话1和会话2在cn2-1上执行，会话3操作在cn2-2上执行，即可实现跨CN终止SQL。

### 10. ignore standby mismatched redo

该语句用于忽略备库与主库的不匹配的redo日志，使备库从redo日志分歧点继续接收主库日志。此操作前提是备库状态处于REDO MISMATCH，处于该状态的备库无法接收主库日志。

示例

```sql
ALTER SYSTEM IGNORE STANDBY MISMATCHED REDO;
```

<span id="dumpclause" name="dumpclause" class="yaslink"></span>

### 11. dump\_clause

该语句用于将YashanDB内部的信息转储（dump）到跟踪（trace）文件中，供用户进行分析和判断故障。

生成的trace文件位于数据库diag/trace目录下，diag目录的位置可通过DIAGNOSTIC_DEST参数进行配置（默认位于{YASDB_DATA}目录下）。

**dump**

用户在dump操作中指定某类内部信息，当前会话中的该类信息就被转储到trace文件中。

每一次dump操作在trace文件中产生一段信息，一个会话中可以多次执行dump操作。

**trace**

trace文件在会话首次执行dump时创建，文件存放在diag/trace目录下，文件名称为yashandb_{yyyymmdd}_{sid}.trc。

trace文件被创建后，后续该会话中所有被dump的信息都被写入该文件。

当会话ID被复用时，sid对应的trace文件也将被复用，不会创建新的trace文件。

> **Note**: 
>
> 当dump的对象是redo type时，trace文件名固定为"yas_redo_type.trc"。

#### 11.1. private log

将当前会话的私有日志信息dump到trace文件中。执行此类信息的dump要求数据库处于mount或open状态。

#### 11.2. logfile

将某个日志文件的页面信息dump到trace文件中。执行此类信息的dump要求数据库处于mount或open状态。

本操作会对日志文件加锁，多个会话同时dump同一个日志文件时会形成锁等待。

当dump的logfile中的某个group是关于加密表空间上的页面记录时，这些group会受到保护，不会解析。

**file_name**

日志名称，为V$LOGFILE视图中存在的某个日志名称。

#### 11.3. datafile

将某个数据文件的页面信息dump到trace文件中。执行此类信息的dump要求数据库处于mount或open状态。

本操作会对数据文件加锁，多个会话同时dump同一个数据文件时会形成锁等待。

当dump加密表空间的数据文件某些页面或整个数据文件时，这些页面会受到保护，不会解析。

当页面在缓存中存在时，会dump内存信息和页面信息，反之则只会dump页面信息。

**file_id**

指定数据文件的ID，该值为数据文件的全局ID，即在V$DATAFILE视图中的ID字段值。

**block_id**

指定数据文件内的页面ID：

- 不指定BLOCK：dump所有页面。
- BLOCK block_id：指定一个页面。
- MINBLOCK block_id [MAXBLOCK block_id]：指定一批页面。必须同时指定MINBLOCK和MAXBLOCK，否则会报错。
- 指定一批页面时，MAXBLOCK小于MINBLOCK或MAXBLOCK超出文件大小均会报错。
#### 11.4. session backtrace

将指定会话的当前堆栈信息dump到trace文件中。执行此类信息的dump要求数据库处于mount或open状态。

本操作不允许并发，同一时间只能有一个会话执行此类dump。

#### 11.5. redo type

将数据库的所有redo类型信息dump到trace文件中，trace文件名为"yas_redo_type.trc"。redo类型信息中，Id列表示redo的类型编号，Type列表示类型名称，Size列表示该redo类型的长度，如果长度为-1，表示变长redo。

示例

```sql
ALTER SYSTEM DUMP PRIVATE LOG;

ALTER SYSTEM DUMP LOGFILE 'redo1';

ALTER SYSTEM DUMP DATAFILE 6;

ALTER SYSTEM DUMP DATAFILE 6 BLOCK 0;

ALTER SYSTEM DUMP DATAFILE 6 MINBLOCK 128 MAXBLOCK 137;

ALTER SYSTEM DUMP SESSION 23 BACKTRACE;

ALTER SYSTEM DUMP REDO TYPE;
```

### 12. flush gts

该语句用于强制同步GTS服务的SCN到所有CN节点。单机部署中不可使用本语句。存算一体分布式集群部署，只有CN节点才能使用本语句。

示例（存算一体分布式集群部署）

```sql
ALTER SYSTEM FLUSH GTS;
```

### 13. clean residual tablespace

该语句用于手动清理迁移chunk后残留的表空间，只能在存算一体分布式集群部署中使用。

示例（存算一体分布式集群部署）

```sql
ALTER SYSTEM CLEAN RESIDUAL TABLESPACE;
```

### 14. clean message pool

该语句用于手动清理集群消息池，只能在共享集群/分布式集群部署中使用。

示例（共享集群/分布式集群部署）

```sql
ALTER SYSTEM CLEAN MESSAGE POOL;
```
