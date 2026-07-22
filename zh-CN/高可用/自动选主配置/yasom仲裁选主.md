## 功能介绍

yasom仲裁选主是指基于yasom进程的[仲裁选主](../../工具手册/yasboot/yasboot命令介绍/yasboot election.md)功能保障业务连续性，当主库发生异常不能对外服务时，系统通过yasom仲裁将备库升为主库，并将原主库降为备库。

yasom仲裁选主仅在yasom和备库的yasagent进程在线时生效。

开启yasom仲裁选主后，yasom进程会对数据库状态进行监控，当主库出现故障时，在备库执行failover；当备库出现故障最大保护模式下主库业务阻塞时，将主库保护模式降级。主库启动前会连接yasom或备库确认实际角色：

- 若连接失败则主库启动失败。

- 若发现已有新主，则需降备，即原主库直接以备库角色启动。

仲裁的模式分为零丢失模式和普通模式，数据库对故障的表现因模式不同而存在差异。

### 零丢失模式

开启零丢失模式的yasom仲裁选主后，主备[保护模式](../YashanDB高可用概述.md#protection_mode)支持最大保护和最大可用，且yasom会自动将其调整为最大保护。

- yasom会持续检测备库连接主库是否超时，若时长超过FailoverThreshold参数值且yasom也与主库断连，则判定主库异常。此时，触发failover流程，yasom将根据候选备库集（FailoverTarget参数）的优先级将目标备库升主。

- 旧主库宕机后重启时会与yasom确认角色，如果确认已有新的主库，旧主库将以备库角色启动。

- 当备库发生故障时，yasom会调整主库的[同步备配置](../定义同步备.md)（REQUIRED_SYNC_STANDBYS参数），将故障备库从同步备列表中剔除，避免该故障备库阻塞主库事务。若该备库恢复正常且完成主备数据同步，yasom会还原主库的同步备配置。

- 当候选备库集中的所有备库均发生故障时，yasom会将主库的保护模式切换为最大可用，避免业务出现阻塞，同时禁用自动failover功能（最大可用模式存在数据丢失风险）。待FailoverTarget中任意备库恢复正常、主备数据同步完成后，yasom会将主库保护模式恢复为最大保护模式，并重新启用自动failover功能。

### 普通模式

开启普通模式的yasom仲裁选主后，主备保护模式支持最大可用和最大性能。该模式优先保证可用性，切换限制少，只要有备库存活都可以在主库故障的情况下进行切换，但不保证数据不丢失。

- yasom会持续检测备库连接主库是否超时，若时长超过FailoverThreshold参数值且yasom也与主库断连，则判定主库异常。此时，触发failover流程，yasom将根据候选备库集（FailoverTarget参数）的优先级将目标备库升主。

- 旧主库宕机后重启时会与yasom确认角色，如果确认已有新的主库，旧主库将以备库角色启动。


<span id="Applicable" name="Applicable"></span>

## 适用场景

|  部署形态| 高可用部署规模|
|--------------------|--------------------------------------|
| 单机部署 | 一主一备部署 |
| 存算一体分布式集群部署 | DN组内节点一主一备部署 |
| 共享集群部署 | 一主一备<br/>一主多备 |

为统一称呼，存算一体分布式集群部署环境的主库指的是DN组的主节点，备库指的是DN组的备节点。共享集群部署环境的主库指的是主集群，备库指的是备集群。

## 前提条件

需确保[操作系统认证](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)功能已开启（遵循标准安装步骤时默认为开启状态）。

## 配置步骤

### 步骤1：准备环境

部署高可用环境，可以参考[安装部署](../../安装和升级/安装部署/00安装部署.md)中所述的部署步骤直接安装相应环境，或参考[扩缩容](../../安装和升级/扩缩容/00扩缩容.md)构建相应环境，环境准备过程中需注意：

- 备库数量须符合[适用场景](#Applicable)。

- 主备库的各项资源参数建议保持一致，主备库所安装的数据库版本必须一致。

- 需开启[操作系统认证](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（遵循标准安装步骤时默认已开启），才能正常使用仲裁选主功能。

- 建议将yasom部署在独立的服务器，不要与主/备库部署在同一服务器。

> **Caution**: 
>
> - 备库与yasom部署于同一台服务器时，不建议使用仲裁选主。如仍需使用，推荐使用普通模式。因为在零丢失模式下，若yasom和备库同时故障，主库的最大保护模式将无法降级，会使主库产生业务阻塞。
>
> - 若yasom不可用，即使处于仲裁选主的零丢失模式，仍有可能因数据库保护模式无法变更（例如，此时备库宕机，主库的保护模式无法从最大保护变为最大可用），而使得主库产生业务阻塞。

环境准备完成后，可以检查数据库部署规模是否符合要求：

```shell
$ yasboot cluster status -c yashandb -d

# 单机一主一备环境回显示例：
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+

# 分布式DN组内节点一主一备环境回显示例：
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                        |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | mn        | 1-1:1  | 56961 | open            | normal          | primary       | 192.168.1.2:1678 | /data/yashan/yasdb_data/mn-1-1   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | dn        | 3-2:7  | 57029 | open            | normal          | standby       | 192.168.1.2:1698 | /data/yashan/yasdb_data/dn-3-2   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0002 | mn        | 1-2:2  | 57024 | open            | normal          | standby       | 192.168.1.3:1678 | /data/yashan/yasdb_data/mn-1-2   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | cn        | 2-1:4  | 57024 | open            | normal          | primary       | 192.168.1.3:1688 | /data/yashan/yasdb_data/cn-2-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0003 | mn        | 1-3:3  | 57021 | open            | normal          | standby       | 192.168.1.4:1678 | /data/yashan/yasdb_data/mn-1-3   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | cn        | 2-2:5  | 57021 | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/cn-2-2   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | dn        | 3-1:6  | 57021 | open            | normal          | primary       | 192.168.1.4:1698 | /data/yashan/yasdb_data/dn-3-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+

# 共享集群一主一备环境示例：
+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
+ hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address     | source_node | data_path                      |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------+
+ host0001 | ce        | 1-1:1  | 19513 | open            | normal          | primary       | 192.168.1.2:1688   | -           | /data/yashan/yasdb_data/ce-1-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+-------------+--------------------------------+
+ host0002 | ce        | 1-2:2  | 19608 | open            | normal          | primary       | 192.168.1.3:1688   | -           | /data/yashan/yasdb_data/ce-1-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+-------------+--------------------------------+
+ host0003 | ce        | 2-1:3  | 19703 | open            | normal          | standby       | 192.168.1.4:1688   | 1-2:2       | /data/yashan/yasdb_data/ce-2-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+-------------+--------------------------------+
+ host0004 | ce        | 2-2:4  | 19651 | open            | normal          | standby       | 192.168.1.5:1688   | unknown     | /data/yashan/yasdb_data/ce-2-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+----------------------------------------------+

# 共享集群一主多备环境示例：
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | source_node | data_path                      |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | ce        | 1-1:1  | 7100  | open            | normal          | primary       | 192.168.0.1:1688 | -           | /data/yashan/yasdb_data/ce-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
| HOST0002 | ce        | 1-2:2  | 7233  | open            | normal          | primary       | 192.168.0.2:1688 | -           | /data/yashan/yasdb_data/ce-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
| HOST0003 | ce        | 2-1:1  | 16433 | open            | normal          | standby       | 192.168.0.3:1688 | 1-2:2       | /data/yashan/yasdb_data/ce-2-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
| HOST0004 | ce        | 2-2:2  | 17331 | open            | normal          | standby       | 192.168.0.4:1688 | unknown     | /data/yashan/yasdb_data/ce-2-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
| HOST0005 | ce        | 3-1:1  | 28469 | open            | normal          | standby       | 192.168.0.5:1688 | 1-2:2       | /data/yashan/yasdb_data/ce-3-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
| HOST0006 | ce        | 3-2:2  | 28676 | open            | normal          | standby       | 192.168.0.6:1688 | unknown     | /data/yashan/yasdb_data/ce-3-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+-------------+--------------------------------+
```

### 步骤2：配置yasom仲裁选主参数



仲裁选主相关的参数如下表所示。

|  参数名| 默认值| 取值范围/格式| 含义|
| ----------------------- | ------ | ---------- | --------------------------- |
| FailoverThreshold       | 9      | [2, 1000]  | 备节点心跳超时时间，到达该时间后，yasom将执行failover切换流程 |
| FailoverTarget          | - 主：所有备（非级联备）<br />- 备：主 | 格式为group&#124;node n:(x,y,z…) | 指定备升主的目标候选组/节点及其优先级顺序<br />- group：用于标识后续配置的数值编号是组ID，适用于共享集群部署。可通过yasboot cluster status命令查看，nodeId短横线前的数值为组ID<br />- node：用于标识后续配置的数值编号是节点ID，适用于单机部署、存算一体分布式集群部署。可通过yasboot cluster status命令查看，nodeId短横线前为节点ID<br />- `n:(x,y,z…)`：使用相应ID值指定具体的目标组/节点及其优先级顺序，优先级遵循配置时的先后顺序。若括号内置空`n:()`则表示将n从所有组/节点的FailoverTarget默认值中移除<br />例如，`node 1:(2,3)`表示节点1的故障转移候选为节点2和节点3，且第一优先级为节点2 |
| FailoverAutoReinstate   | false  | true/false | 是否启用自动脑裂修复。<br/>启用后，如果备节点发生脑裂，处于NEED REPAIR状态，yasom将尝试自动修复 |
| ZeroDataLossMode        | true   | true/false | 是否启用零丢失模式。<br>启用后，将设置主备为最大保护模式，当主节点宕机时，备节点可自动failover；当备节点异常时，主节点将由yasom降级为最大可用模式，并禁止自动failover，直到备节点恢复同步后，yasom重新将主节点升级为最大保护模式后，可以自动failover |

> **Caution**: 
>
> - FailoverThreshold太小，可能会因为网络抖动而发生不必要的切换，请根据网络状态设置合理的超时时间。
> - FailoverAutoReinstate启用后，会自动修复备节点脑裂问题，会使备节点与主节点有分歧的部分数据丢失，请**谨慎使用**。
> - ZeroDataLossMode启用后，优先使用最大保护模式。在最大保护模式下，主节点宕机，备节点自动failover后，不会丢失数据。当备节点异常后，主节点会降级为最大可用模式，此时备节点有丢失数据的风险，所以自动failover将禁用，直到主节点再次恢复最大保护模式。因此零丢失模式的切换条件更严格，但是能保证数据不丢失。



1. 执行如下命令，配置yasom仲裁选主参数：

      ```shell
    # 本文仅为示例值，请根据实际需求调整
    $ yasboot election config set -k FailoverThreshold -v 5 -c yashandb
    $ yasboot election config set -k ZeroDataLossMode -v true -c yashandb

    # 在共享集群中，需用组ID配置FailoverTarget参数
    $ yasboot election config set -k FailoverTarget  -v "group 1:(2,3)"  -c yashandb
    
    # 在单机部署、存算一体分布式集群部署中，需用节点ID配置FailoverTarget参数
    $ yasboot election config set -k FailoverTarget  -v "node 1:(2,3)"  -c yashandb
    ```

2. 查看FailoverTarget配置：
     
    ```shell
    $ yasboot election target show -c yashandb
    cluster
    +------------------------------------+
    | group id | target group id | seted | 
    +------------------------------------+
    | 1        | 2               | false | 
    +----------+-----------------+-------+
    | 2        | 1               | false |
    +----------+                 +-------+
    | 3        |                 | true  | 
    +----------+-----------------+-------+
    ```

3. 查看yasom仲裁选主参数是否配置正确，数据库状态是否正常：

    ```shell
    $ yasboot election config show -c yashandb
    ```

    回显信息示例：

    ::: tabs
    == 单机部署

```shell
group 1
  Protection Mode: MAXIMUM AVAILABILITY
  Members:
    [1-1:1] - Primary database
      [1-2:2] - Physical standby database
                  Transport Lag: 0 seconds
                  Apply Lag:     15 seconds
                  Apply Rate:    0.00 KByte/s

  Properties:
    FailoverThreshold      = 5
    FailoverAutoReinstate  = false
    ZeroDataLossMode       = true

  Configurable Failover Conditions:
    Health Conditions:
      CORRUPTED_CONTROLFILE          = NO
      INACCESSIBLE_LOGFILE           = NO
      STUCK_ARCHIVER                 = NO
      DATAFILE_WRITE_ERRORS          = NO
      DATAFILE_NO_SPACE              = NO
      SLICE_NO_SPACE                 = NO
    Error Code Conditions:
      (null)

Automatic Failover: DISABLED
```

    == 共享集群部署

```shell
cluster
    Protection Mode: MAXIMUM AVAILABILITY
    Members:
      [1-1:1] - Primary database
      [1-2:2] - Primary database
        [2-1:1] - (*) Physical standby database
                  Transport Lag: 0 seconds
                  Apply Lag:     0 seconds
                  Apply Rate:    460.37 MByte/s
      [2-2:2] - (*) Physical standby database
      [3-1:1] - Physical standby database
                  Transport Lag: 0 seconds
                  Apply Lag:     0 seconds
                  Apply Rate:    106.33 MByte/s
      [3-2:2] - Physical standby database
    (*) Failover target 

  Properties:
    FailoverThreshold      = 5
    FailoverAutoReinstate  = false
    ZeroDataLossMode       = true

  Configurable Failover Conditions:
    Health Conditions:
      CORRUPTED_CONTROLFILE          = NO
      INACCESSIBLE_LOGFILE           = NO
      STUCK_ARCHIVER                 = NO
      DATAFILE_WRITE_ERRORS          = NO
      DATAFILE_NO_SPACE              = NO
      SLICE_NO_SPACE                 = NO
    Error Code Conditions:
      (null)

Automatic Failover: DISABLED
```
    == 存算一体分布式集群部署

```shell
group 3
Protection Mode: MAXIMUM AVAILABILITY
Members:
  [3-1:7] - Primary database
    [3-2:8] - Physical standby database
                Transport Lag: 0 seconds
                Apply Lag:     15 seconds
                Apply Rate:    0.00 KByte/s

Properties:
  FailoverThreshold      = 5
  FailoverAutoReinstate  = false
  ZeroDataLossMode       = true

Configurable Failover Conditions:
    Health Conditions:
      CORRUPTED_CONTROLFILE          = NO
      INACCESSIBLE_LOGFILE           = NO
      STUCK_ARCHIVER                 = NO
      DATAFILE_WRITE_ERRORS          = NO
      DATAFILE_NO_SPACE              = NO
      SLICE_NO_SPACE                 = NO
    Error Code Conditions:
      (null)

Automatic Failover: DISABLED
```
    :::

    回显信息中的各字段含义如下表所示。

    |  字段| 含义|
    | --------------------- |-----------------------------------------------------------|
    | group n<br />cluster | 参与yasom仲裁的对象类型：<br />- cluster：表示当前为集群级别的yasom仲裁，即环境为共享集群部署<br />- group n：表示当前为节点级别的yasom仲裁，即环境为单机部署或存算一体分布式集群部署，n为节点所属组的ID |
    | Protection Mode | yasom记录的主节点保护模式。 |
    | Members | 参与仲裁的节点/组信息，包括节点状态、节点角色、备节点传输延迟、回放延迟和回放速率等。 |
    | Database Error(s) | 记录高可用相关异常，例如某个备库的redo日志与主库不匹配。 |
    | Properties | 当前已生效的参数信息。 |
    | Configurable Failover Conditions | 主库的条件故障切换配置项，Health Conditions对应于主库的配置参数FAILOVER_HEALTH_CONDITION，Error Code Conditions对应于主库的配置参数FAILOVER_ERROR_CONDITION，并且只显示有效的错误码，更多详情请查阅[配置条件故障切换](#failover_error_condition)。 |
    | Automatic Failover | yasom仲裁选主的开启状态：<br />- DISABLED：yasom仲裁已关闭。<br />- Enabled in Potential Data Loss Mode：普通模式的yasom仲裁选主已开启。<br />- Enabled in Zero Data Loss Mode：零丢失模式的yasom仲裁选主已开启。<br />- Enabled in Zero Data Loss Mode (NOT ALLOWED)：零丢失模式的yasom仲裁选主已开启，但数据库的保护模式不是最大保护，无法自动切换。 |

    

根据返回信息确认yasom仲裁选主相关参数是否已生效，并且主备状态正常，再执行后续操作。

### 步骤3：开启yasom仲裁选主

开启仲裁选主前需确保主备库状态均正常且保护模式相同。在共享集群部署下，还要确保所有实例均已启动，且主备集群各自至少有1个实例处于OPEN阶段。

1. 执行如下命令，开启yasom仲裁选主：

    ```shell 
    $ yasboot election enable on -c yashandb
    ```

2. 查看yasom仲裁选主状态：

    ```shell
    $ yasboot election status -c yashandb
    ```

    

回显信息中的各字段含义如下表所示。
    
|  字段| 含义|
| --------------------- |-----------------------------------------------------------|
| group n<br />cluster | 参与yasom仲裁的对象类型：<br />- cluster：表示当前为集群级别的yasom仲裁，即环境为共享集群部署<br />- group n：表示当前为节点级别的yasom仲裁，即环境为单机部署或存算一体分布式集群部署，n为节点所属组的ID |
| Protection Mode | yasom记录的主节点保护模式。 |
| Members | 参与仲裁的节点/组信息，包括节点状态、节点角色、备节点传输延迟、回放延迟和回放速率等。 |
| Database Error(s) | 记录高可用相关异常，例如某个备库的redo日志与主库不匹配。 |
| Automatic Failover | yasom仲裁选主的开启状态：<br />- DISABLED：yasom仲裁选主已关闭。<br />- Enabled in Potential Data Loss Mode：普通模式的yasom仲裁选主已开启。<br />- Enabled in Zero Data Loss Mode：零丢失模式的yasom仲裁选主已开启。<br />- Enabled in Zero Data Loss Mode (NOT ALLOWED)：零丢失模式的yasom仲裁选主已开启，但数据库的保护模式不是最大保护，无法自动切换。 |



“Enabled in Zero Data Loss Mode”表示已在零丢失模式下开启yasom仲裁选主，主备的保护模式会自动改为最大保护，此状态下允许自动failover（如果主库发生故障，备库会自动failover升主）。

<span id="failover_error_condition" name="failover_error_condition"></span>

### 步骤4（可选）：配置条件故障切换

条件故障切换是可选配置，可根据实际需求进行选配。

在某些场景中，为防止主库可能因出现特定异常而无法继续提供服务（但主备间网络依旧正常），可以通过事先指定自动切换的条件适时触发自动选主，更针对性地保障数据库持续提供服务。条件故障切换有两种类型，分别由以下两个参数控制：

- FAILOVER_ERROR_CONDITION：可以配置主库向客户端抛出某些错误码时，主库立即关闭。错误码可以为数据库错误码，也可以为数据库错误码+系统错误码的组合。建议避免选择高频率的错误码以免过于频繁地触发自动切换。对文件类操作的异常，数据库错误码可能相同但系统错误码不同，建议采用“数据库错误码-系统错误码”格式更精准地指定触发条件。

- FAILOVER_HEALTH_CONDITION：可以配置主库出现某些异常场景时，主库立即关闭。包括归档磁盘满，redo刷盘失败，dbwr刷脏页失败等。
  * CORRUPTED_CONTROLFILE：表示主库写控制文件失败。

  * INACCESSIBLE_LOGFILE：表示主库写redo文件失败。

  * STUCK_ARCHIVER：表示主库因磁盘满导致归档失败。
    
  * DATAFILE_WRITE_ERRORS：表示主库写脏页失败。

  * DATAFILE_NO_SPACE：表示主库因磁盘满导致创建或写入数据文件失败。

  * SLICE_NO_SPACE：表示主库因磁盘满导致创建或写入slice文件失败。

条件故障切换的流程如下：

1. 按需配置自动切换的条件。

    ```sql
    -- 本文仅为示例，请根据实际需求配置
    -- 设置主库发生redo写失败或者dbwr刷脏页写失败后，关闭主库并触发选举
    ALTER SYSTEM SET FAILOVER_HEALTH_CONDITION = 'INACCESSIBLE_LOGFILE, DATAFILE_WRITE_ERRORS' SCOPE = BOTH;

    -- 设置主库抛出数据库错误为311时，关闭主库并触发选举
    ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311' SCOPE = BOTH;

    -- 设置主库抛出数据库错误为311并且对应的系统错误码为28时，关闭主库并触发选举
    ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311-28' SCOPE = BOTH;
    ```

    >  **Note**: 
    >
    >  仅当开启yasom仲裁选主时，条件故障切换参数相关配置会在数据库为主库时生效，并且不会自动同步给备库。

2. 一旦主库在运行中出现切换条件所指定的异常或错误，主库会尝试同步剩余redo日志给备库，然后立即关闭（终止YASDB进程）。

3. 备库将触发心跳超时，yasom下发failover指令给备库，让其升主。

## 常见问题

#### 如何规划FailoverTarget备库的性能要求？

建议选用网络优质、性能优良、与主库同地域的直连物理备库作为FailoverTarget。最大保护、最大可用模式下，该备库会优先作为同步备库，异地或高网络延迟的备库可能导致主库性能下降。

#### 如何剔除异地、高延迟备库？

若未配置FailoverTarget，yasom仲裁选主功能开启时将使用默认值配置FailoverTarget。如需从默认值中剔除异地、高延迟备库，可使用`group group_id:()`或`node node_id:()`将对应节点的候选备库集设置为空集合。配置生效后，该节点将从所有节点的FailoverTarget默认值中移除。

```shell
# 在共享集群部署中，剔除某个备集群需使用该集群的组ID
$ yasboot election config set -k FailoverTarget  -v "group 3:()"  -c yashandb

# 在单机部署、存算一体分布式集群部署中，剔除某个备库/节点需使用其节点ID
$ yasboot election config set -k FailoverTarget  -v "node 3:()"  -c yashandb
```

#### 如何查看yasom仲裁选主记录？

使用yasboot election event命令可以查看yasom仲裁选主的记录，包括failover，保护模式切换，脑裂修复，数据库启动角色确认等。

#### yasom仲裁选主启动失败该如何处理？

开启yasom仲裁选主功能的前提是主备库状态正常。若功能启动失败，请检查主备连接状态，以及每个数据库v$database的STATUS是否为NORMAL。如果数据库不正常，请通过查询v$diag_incident，查看告警日志，运行日志等定位，常见的故障可能有磁盘空间不足，网络故障等。

#### 为什么无法切换到最大保护模式？

切换到最大保护模式时要求主库的redo已完全同步给备库。如果备库故障或备库redo接收速度慢，都会导致redo无法同步，请检查备库状态是否正常，网络带宽是否充足，并保证足够的备库的硬件资源。

#### 备库Apply Lag越来越大该怎么处理？

备库回放速度慢，跟不上主库redo产生速度时，备库Apply Lag会越来越大。请保证备库拥有足够的硬件资源（不要远低于主库的资源），包括CPU、内存以及磁盘性能等。也可以尝试修改并行参数，提高并行度。

#### 主库故障后，长时间无法升主的可能原因有哪些？

- 可能是备库回放速度慢，有大量的redo等待回放。可以通过查询v$database的switchover_status确认备库是否在升主，如果是，请等待redo回放完毕。

- 可能yasom进程异常，例如yasom进程被关闭、主库所在服务器完全宕机、yasom进程的主备配置不正确等。此时需根据情况[修复yasom故障](../../工具手册/yasboot/管理yasom/yasom常见故障处理.md)。

- 备库状态异常，不可升主。检查备库的yasagent进程是否在线、备库是否出现need repair、是否磁盘空间满。

- 零丢失模式下，如果数据库处于最大可用模式是禁止自动failover的，如需强制failover，请强制关闭yasom仲裁后手动执行failover。

#### 强制关闭yasom仲裁选主后，主库重启报确认角色失败该怎么处理？

强制关闭yasom仲裁选主功能时会尝试重置每个数据库上的OM_ELECTION_ENABLE参数，如果数据库不可访问，会导致参数残留。该主库重启后，无法通过yasom确认角色，会启动失败。

请手动修改主备的OM_ELECTION_ENABLE参数。

#### 存算一体分布式集群部署下，如何配置和管理某几个DN组的仲裁选主？

在yasboot election的命令中使用--group-ids可以指定DN组，不指定该参数选项表示对所有DN组生效。

#### 使用yasboot stop命令关闭主库后备库不会自动升主，但为什么使用SQL语句关闭主库后备库会自动升主？

yasom会将通过yasboot下发stop、restart等命令视作用户主动发起的运维操作而非故障场景，因此不会下发failover命令升主。如果直接使用SQL语句关闭数据库，因yasom无法感知，会将其一律视作故障场景，从而触发自动选主。

在启用yasom仲裁选主功能后，建议使用yasboot命令进行节点启停等运维操作，避免不必要的故障切换。
