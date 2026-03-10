YashanDB的redo日志文件用于记录数据库产生的物理日志，可被用于数据库宕机重演和主备复制。

## 查看redo日志文件

### 查看物理文件

使用安装用户登录数据库所在服务器，进入redo日志文件的存放路径查看对应的物理文件，redo日志文件存放在$YASDB_DATA/dbfiles目录。

```shell
$ cd $YASDB_DATA/dbfiles
$ ls -lrt redo*
total 15456
-rw-r----- 1 yashan yashan 104857600 Jun 16 09:49 redo2
-rw-r----- 1 yashan yashan 104857600 Jun 16 09:49 redo3
-rw-r----- 1 yashan yashan 104857600 Jun 17 11:45 redo0
-rw-r----- 1 yashan yashan 104857600 Jun 18 15:15 redo1
```

### 通过视图查看

通过[V$LOGFILE](../../参考手册/系统视图/动态视图/V$LOGFILE)视图可以查看redo日志文件的信息。



```sql
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 CURRENT
```


redo日志文件的状态包括NEW、CURRENT、ACTIVE和INACTIVE，详细介绍如下表所示。

|  状态| 说明|
|--------------------|---------------------|
|NEW  |  未使用过的新文件。  |
|CURRENT  |  当前正在写入的文件。<br/>当文件写满（即自动切换）或手动切换时，会选取一个NEW或INACTIVE状态的文件作为下一个CURRENT文件，原CURRENT文件会变为ACTIVE状态。<br/>如不存在NEW或INACTIVE状态的文件则会触发checkpoint。  |
|ACTIVE  |  刚从CURRENT状态完成切换的文件，文件中可能存在部分内容暂未写入磁盘，或暂未归档（若已开启归档模式）。<br/>执行checkpoint并等待归档完成（若已开启归档模式）后，会变成INACTIVE状态。  |
|INACTIVE  |  文件内容已全部写入磁盘，且已归档（若已开启归档模式）。  |

## 管理redo日志文件

YashanDB要求至少并存3个redo日志文件，且会默认创建3个名称为redo1/redo2/redo3，大小为128M，BLOCKSIZE为4K的redo日志文件。

redo日志文件是循环复用的，文件数量、单个文件的大小等均与数据库性能挂钩，且无法直接扩展redo日志的大小（需通过添加新的大文件+删除原有小文件的方式实现），应根据业务需求进行合理规划，详情请查阅[数据库配置调优](../../性能调优/数据库性能基础/数据库配置调优)。

在共享集群部署中，redo日志文件是各个实例独享的，添加/删除redo日志文件时需连接对应实例再操作其专属文件。

### 添加redo日志文件

若初始配置无法满足业务/性能需求，可通过[ALTER DATABASE](../../开发手册/SQL参考手册/SQL语句/ALTER DATABASE.html#add_logfile)语句自定义添加redo日志文件。

#### 前提条件

- 添加redo日志文件要求数据库处于OPEN阶段。

- 已按需规划redo日志文件的配置：

    - filename：文件名，可根据现存文件进行编号顺延。

    - SIZE：redo日志文件大小的最小值受DB_BLOCK_SIZE，MAX_SESSIONS和REDO_BUFFER_SIZE参数影响，最小值参考公式为`DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2`。请结合实际业务需求与预期参数值计算结果，综合评估并确定合适的大小。

    - BLOCKSIZE：文件的块大小，可省略，默认为4096，需指定为512的整数（若非整数倍则创建时自动向上取整）。

#### 操作步骤

1. 以DBA用户连接并登录数据库。
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. 查看现有redo日志文件。

    
  
  ```sql
  SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;
  
  THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
  ------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
        1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
        1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
        1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 CURRENT
  ```
  

3. 执行ALTER DATABASE ADD LOGFILE语句，添加新的redo日志文件。

    ```sql
    ALTER DATABASE ADD LOGFILE ('redo4q' SIZE 200M,'redo5' SIZE 64G BLOCKSIZE 4k,'redo6' SIZE 64G PARALLEL 8);

    SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

    THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
    ------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
          1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
          1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 ACTIVE
          1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 CURRENT
          1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     0            0 NEW
          1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     0            0 NEW
          1            5 $YASDB_DATA/dbfiles/redo6                           4096              16777216                     0            0 NEW
    ```

<span id="SWITCH" name="SWITCH" class="yaslink"></span>

### 切换redo日志文件

redo日志文件的切换方式分为：

- 自动切换：CURRENT状态的redo日志文件写满时，数据库会自动选择一个处于NEW或INACTIVE状态的文件进行切换。如不存在NEW或INACTIVE状态的文件则会触发checkpoint。

- 手动切换：

    - 单切换：执行[ALTER SYSTEM SWITCH LOGFILE](../../开发手册/SQL参考手册/SQL语句/ALTER SYSTEM.html#switch_logfile)语句可强制切换redo日志文件，即使数据库处于归档模式，该操作仍不会自动归档。

    - 归档 + 切换：数据库处于归档模式时，执行[ALTER SYSTEM ARCHIVE LOG CURRENT](../../开发手册/SQL参考手册/SQL语句/ALTER SYSTEM.html#archive_logfile)语句可归档当前CURRENT状态的redo日志文件，同时会切换redo日志文件。

```sql
-- 方式一：强制切换
ALTER SYSTEM SWITCH LOGFILE;

-- 方式二：在归档模式下，归档 + 切换
-- 查看当前数据库的归档模式
SELECT database_name,log_mode FROM V$DATABASE;

DATABASE_NAME    LOG_MODE          
---------------- -----------------
yashandb            ARCHIVELOG      
-- 确认LOG_MODE = ARCHIVELOG后才能执行后续操作
ALTER SYSTEM ARCHIVE LOG CURRENT;

-- 查看redo日志文件信息
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 ACTIVE
      1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     2            4 CURRENT
      1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     0            0 NEW
      1            5 $YASDB_DATA/dbfiles/redo6                           4096              16777216                     0            0 NEW
```

### 删除redo日志文件

如发生下述情况，可通过[ALTER DATABASE](../../开发手册/SQL参考手册/SQL语句/ALTER DATABASE.html#drop_logfile)语句删除redo日志：

- 磁盘发生故障时，可通过删除故障磁盘上redo日志文件避免写入不可访问的文件。

- redo日志文件存储在不恰当的位置。

- 通过增加更大的redo日志文件 + 删除原有的小文件实现redo文件容量扩展。

> **Caution**: 
>
> 为了保证数据库正常运行以及数据安全，仅允许直接删除NEW或INACTIVE状态的redo日志文件。

#### 前提条件

添加redo日志文件要求数据库处于OPEN阶段。

#### 操作步骤

1. 以DBA用户连接并登录数据库。
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. 查看现有redo日志文件。

    ```sql
    SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

    THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
    ------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
          1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
          1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
          1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 ACTIVE
          1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     2            4 CURRENT
          1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     0            0 NEW
          1            5 $YASDB_DATA/dbfiles/redo6                           4096              16777216                     0            0 NEW
    ```

3. 根据目标文件的状态选择操作步骤：

    ::: tabs

    == 处于NEW或INACTIVE状态

    直接执行ALTER DATABASE DROP LOGFILE语句即可删除NEW或INACTIVE状态的redo日志文件。
    
```sql
ALTER DATABASE DROP LOGFILE 'redo6';

SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 ACTIVE
      1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     2            4 CURRENT
      1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     0            0 NEW
```
    == 处于ACTIVE状态

    1. ）执行checkpoint将redo日志文件内容写入磁盘，若已开启归档模式还需等待该文件完成归档，使其变为INACTIVE状态。
    2. ）执行ALTER DATABASE DROP LOGFILE语句删除目标redo日志文件。

```sql
-- 1. 执行checkpoint
ALTER SYSTEM CHECKPOINT;

-- 2. 查看目标redo日志文件的状态，直到状态变为INACTIVE后再执行后续操作
SELECT thread#,id,name,SEQUENCE#,status FROM V$LOGFILE WHERE SEQUENCE# = 3;

THREAD#           ID NAME                                                                SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            2 $YASDB_DATA/dbfiles/redo3                              3 INACTIVE

-- 3. 删除目标redo日志文件
ALTER DATABASE DROP LOGFILE 'redo3';
-- 4. 检查删除结果
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            2 INACTIVE
      1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     2            4 CURRENT
      1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     0            0 NEW
```

    == 处于CURRENT状态

    1. ）手动切换redo日志文件，使其变为ACTIVE状态。
    2. ）执行checkpoint将redo日志文件内容写入磁盘，若已开启归档模式还需等待该文件完成归档，使其变为INACTIVE状态。
    3. ）执行ALTER DATABASE DROP LOGFILE语句删除目标redo日志文件。
```sql
-- 1. 手动切换redo日志文件
-- 查看当前数据库的归档模式
SELECT database_name,log_mode FROM V$DATABASE;

DATABASE_NAME    LOG_MODE          
---------------- -----------------
yashandb            ARCHIVELOG      
-- LOG_MODE = ARCHIVELOG，可直接执行归档 + 切换
ALTER SYSTEM ARCHIVE LOG CURRENT;
-- 查看
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 ACTIVE
      1            3 $YASDB_DATA/dbfiles/redo4                          4096                 51200                     2            4 ACTIVE
      1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     2            5 CURRENT
      1            5 $YASDB_DATA/dbfiles/redo6                           4096              16777216                     0            0 NEW

-- 2. 执行checkpoint
ALTER SYSTEM CHECKPOINT;

-- 3. 查看目标redo日志文件的状态，直到状态变为INACTIVE后再执行后续操作
SELECT thread#,id,name,SEQUENCE#,status FROM V$LOGFILE WHERE SEQUENCE# = 4;

THREAD#           ID NAME                                                                SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            3 $YASDB_DATA/dbfiles/redo4                              4 INACTIVE

-- 4. 删除目标redo日志文件
ALTER DATABASE DROP LOGFILE 'redo4';
-- 5. 检查删除结果
SELECT thread#,id,name,block_size,block_count,used_blocks,SEQUENCE#,status FROM V$LOGFILE;

THREAD#           ID NAME                                                               BLOCK_SIZE           BLOCK_COUNT           USED_BLOCKS    SEQUENCE# STATUS
------- ------------ ---------------------------------------- ------------ --------------------- --------------------- ------------ -----------------
      1            0 $YASDB_DATA/dbfiles/redo1                           4096                 32768                  7482            1 INACTIVE
      1            1 $YASDB_DATA/dbfiles/redo2                           4096                 32768                 32768            2 INACTIVE
      1            2 $YASDB_DATA/dbfiles/redo3                           4096                 32768                  8200            3 INACTIVE
      1            4 $YASDB_DATA/dbfiles/redo5                           4096              16777216                     2            5 CURRENT
      1            5 $YASDB_DATA/dbfiles/redo6                           4096              16777216                     0            0 NEW
```
    :::
