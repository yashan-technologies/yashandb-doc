YashanDB的redo日志文件用于记录数据库产生的物理日志，可被用于数据库宕机重演和主备复制。

redo日志文件有如下4种状态：

- NEW：表示新创建且未使用的redo日志文件。
- CURRENT：表示当前正在写入的redo日志文件，当current redo日志文件写满，或者手动执行切换操作后，会将下一个可用的redo日志文件状态设置为CURRENT，原current redo日志文件状态变为ACTIVE。
- ACTIVE：该状态下的redo日志文件包含的redo对应的页面存在未写入磁盘的情况，或者该redo日志文件没有被归档，不可复用。ACTIVE状态的redo日志文件，在执行checkpoint并等待归档完成（如果已开启归档模式）后会变成INACTIVE状态。
- INACTIVE：该状态下的redo日志文件包含的redo对应的页面都已经写入磁盘，可复用。

> **Note**：
>
> redo日志文件切换时，会将处于INACTIVE或NEW状态的redo日志文件作为下一个文件，如不存在INACTIVE或NEW状态的redo日志文件则会触发checkpoint。

## 查看redo日志文件

> **Note**：
>
> 本文以单机部署为例，不同部署形态的查看方法相同但$YASDB_DATA路径不同，具体请以实际为准。

**方式一**：

通过V$LOGFILE视图查看redo日志的信息，包括它的组号、路径、成员、块大小、已用大小与状态等信息。

示例

```sql
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD# ID NAME                                            BLOCK_SIZE  BLOCK_COUNT  USED_BLOCKS SEQUENCE# STATUS    

------- -- ----------------------------------------------  ---------- ------------ ------------ --------- -------- 

1  0 /data/yashan/yasdb_data/db-1-1/dbfiles/redo0        4096        25600         2961         1 INACTIVE 
1 1 /data/yashan/yasdb_data/db-1-1/dbfiles/redo1        4096        25600          840         2 CURRENT  
1 2 /data/yashan/yasdb_data/db-1-1/dbfiles/redo2        4096        25600            0         0 INACTIVE 
1 3 /data/yashan/yasdb_data/db-1-1/dbfiles/redo3        4096        25600            0         0 INACTIVE 
```

**方式二**：

直接进入到$YASDB_DATA/dbfiles目录查看。

```shell
$ cd /data/yashan/yasdb_data/db-1-1/dbfiles
$ ls -lrt redo*
-rw-r----- 1 yashan yashan 104857600 Jun 16 09:49 redo2
-rw-r----- 1 yashan yashan 104857600 Jun 16 09:49 redo3
-rw-r----- 1 yashan yashan 104857600 Jun 17 11:45 redo0
-rw-r----- 1 yashan yashan 104857600 Jun 18 15:15 redo1
```

## 维护redo日志文件

### 添加redo日志

YashanDB中添加redo日志主要是用于提高数据库性能，具体见[数据库配置调优](../../性能调优/数据库性能基础/数据库配置调优)章节描述。

可通过ALTER DATABASE语句添加redo日志。

示例

```sql
ALTER DATABASE ADD LOGFILE ('redo6a' size 200M,'redo6b' size 200M);
-- 或者
ALTER DATABASE ADD LOGFILE ('redo7a' size 200M  BLOCKSIZE 512,'redo7b' size 200M BLOCKSIZE 512);
```
> **Note**：
>
> - 添加redo日志时，属性size需放在BLOCKSIZE前才能正常添加，默认块大小为4096Byte，这与磁盘类型有关系，有些磁盘并不支持512Byte的块大小，只能设为4096Byte。
> - YashanDB无法直接扩展redo日志的大小，需通过添加新的大redo日志并将原有的小redo日志删除的方式实现。

### 切换redo日志

可通过ALTER SYSTEM语句手动切换redo日志。

示例

```sql
-- 只切换redo
ALTER SYSTEM SWITCH LOGFILE;

-- 切换redo，并且等待该redo归档完成（归档模式下）
ALTER SYSTEM ARCHIVE LOG CURRENT;
```

### 删除redo日志

如发生下述情况，可通过ALTER DATABASE语句删除redo日志：

- 磁盘发生故障时，可通过删除故障磁盘上redo日志文件避免写入不可访问的文件。
- redo日志文件存储在不恰当的位置。
- 通过增加更大的redo文件，删除原有的redo，从而实现redo文件扩展。

> **Caution**：
>
> 为了保证数据库正常运行以及数据安全，仅允许直接删除INACTIVE或NEW状态的redo日志。
>
> 如需删除CURRENT状态的redo日志，操作如下：
>1. 切换redo日志，使其变为ACTIVE状态。
>2. 执行checkpoint并等待归档完成（如果已开启归档模式），使其变为INACTIVE状态。
>3. 执行删除操作。

```sql
-- 建议先执行checkpoint，以便redo文件变成INACTIVE状态
ALTER SYSTEM CHECKPOINT;

-- 本文以单机部署为例，具体请修改为实际的redo日志文件路径
ALTER DATABASE DROP LOGFILE '/data/yashan/yasdb_data/db-1-1/dbfiles/redo5a';
```
