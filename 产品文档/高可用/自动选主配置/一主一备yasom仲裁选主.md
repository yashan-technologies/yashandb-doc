## 功能介绍

yasom仲裁选主是指基于yasom进程的[仲裁选主](../../工具手册/yasboot/yasboot命令介绍/yasboot election)功能保障业务连续性，当主库发生异常不能对外服务时，系统通过yasom仲裁将备库升为主库，并将原主库降为备库。

开启仲裁选主后，主库启动前会连接yasom或备库确认实际角色，若连接失败则主库启动失败。如需降备，原主库会直接以备库角色启动。正常运行中，yasom进程会对数据库状态进行监控，当主库出现故障时，在备库执行failover；当备库出现故障最大保护模式下主库业务阻塞时，将主库保护模式降级。

仲裁选主仅在yasom和备库的yasagent进程在线时生效。

仲裁的模式分为零丢失模式和普通模式，具体表现如下：

- 零丢失模式（ZeroDataLossMode=true）：主备[保护模式](../保护模式切换)支持最大保护和最大可用，开启零丢失模式的仲裁选主后，yasom会自动将保护模式调整为最大保护。处于最大保护模式的场景下，主库发生故障时yasom会执行自动failover切换。若某个备库异常，主库会降为最大可用模式，若此时主库异常，为确保备库不丢数据（即RPO=0），yasom不会自动failover。

- 普通模式（ZeroDataLossMode=false）：主备保护模式支持最大可用和最大性能。切换限制少，但不保证RPO=0，该模式可能会导致数据丢失。

## 适用场景

| 部署形态              | 高可用部署规模                                |
|--------------------|--------------------------------------|
| 单机部署 | 一主一备部署 |
| 分布式部署 | DN组内节点一主一备部署 |

## 前提条件

需确保[操作系统认证](../../数据库管理/基本数据库管理/操作系统身份认证配置)功能已开启（遵循标准安装步骤时默认为开启状态）。

## 配置步骤

### 步骤1：准备一主一备环境

部署一主一备高可用环境，可以参考[安装部署](../../安装和升级/安装部署/00安装部署)中所述的单机（主备）部署步骤直接安装相应环境，或参考[扩缩容](../../安装和升级/扩缩容/00扩缩容)构建相应环境，环境准备过程中需注意：

- 备库数量仅允许1个。

- 主备库的各项资源参数建议保持一致，主备库所安装的数据库版本必须一致。

- 需开启[操作系统身份认证](../../数据库管理/基本数据库管理/操作系统身份认证配置)（遵循标准安装步骤时默认已开启），才能正常使用仲裁选主功能。

- 建议将yasom部署在独立的服务器，不要与主/备库部署在同一服务器。

> **Caution**：
>
> - 备库与yasom部署于同一台服务器时，不建议使用仲裁选主。如仍需使用，推荐使用普通模式。因为在零丢失模式下，若yasom和备库同时故障，主库的最大保护模式将无法降级，会使主库产生业务阻塞。
>
> - 若yasom不可用，即使处于仲裁选主的零丢失模式，仍有可能因数据库保护模式无法变更（例如，此时备库宕机，主库的保护模式无法从最大保护变为最大可用），而使得主库产生业务阻塞。

环境准备完成后，可以检查数据库部署规模是否符合要求：

```shell
$ yasboot cluster status -c yashandb -d

# 单机一主一备环境示例：
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+

# 分布式DN组内节点一主一备环境示例：
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
```

### 步骤2：配置yasom仲裁选主参数

yasom仲裁选主的相关参数如下：

| 参数名                   | 默认值   | 取值范围    |           参数说明               |
| ----------------------- | ------ | ---------- | --------------------------- |
| FailoverThreshold       | 9      | [2,1000]  | 备库心跳超时时间（单位：秒），到达该时间后，yasom将执行failover切换流程 |
| FailoverAutoReinstate   | false  | [true&#124;false] | 是否启用自动脑裂修复。<br/>启用后，如果备库发生脑裂处于NEED REPAIR状态，yasom将尝试自动修复。 |
| ZeroDataLossMode        | true   | [true&#124;false] | 是否启用零丢失模式。<br/>启用后，自动将主备设置为最大保护模式。<br/>* 如果主库宕机，备库可自动failover。<br/>* 如果备库异常，yasom将把主库的保护模式降级为最大可用并禁止自动failover，直到备库恢复同步后，yasom才会重新将主库恢复为最大保护模式并允许自动failover。 |


1. 执行如下命令，配置yasom仲裁选主参数：

    ```shell
    # 本文仅为示例值，请根据实际需求调整
    $ yasboot election config set -k FailoverThreshold -v 5 -c yashandb
    $ yasboot election config set -k ZeroDataLossMode -v true -c yashandb
    ```

2. 查看yasom仲裁选主参数是否配置正确，数据库状态是否正常：

    ```shell
    $ yasboot election config show -c yashandb

    # 单机一主一备环境回显示例：
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

    # 分布式DN组内节点一主一备环境回显示例：
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

    根据返回信息可知，仲裁选主相关参数已生效，并且主备状态正常，没有显示告警。

### 步骤3：开启yasom仲裁选主

开启仲裁选主前需确保主备库状态均正常且保护模式相同。

1. 执行如下命令，开启仲裁选主：

    ```shell 
    $ yasboot election enable on -c yashandb
    ```

2. 查看yasom仲裁选主状态：

    ```shell
    $ yasboot election status -c yashandb

    # 单机一主一备环境回显示例：
    group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     15 seconds
                      Apply Rate:    0.00 KByte/s

    Automatic Failover: Enabled in Zero Data Loss Mode

    # 分布式DN组内节点一主一备环境回显示例：
    group 3
    Protection Mode: MAXIMUM PROTECTION
    Members:
      [3-1:7] - Primary database
        [3-2:8] - Physical standby database
                    Transport Lag: 0 seconds
                    Apply Lag:     15 seconds
                    Apply Rate:    0.00 KByte/s

    Automatic Failover: Enabled in Zero Data Loss Mode
    ```

    根据返回信息，“Enabled in Zero Data Loss Mode”表示已在零丢失模式下开启yasom仲裁选主，主备的保护模式会自动改为最大保护，此状态下允许自动failover（如果主库发生故障，备库会自动failover升主）。

在当前配置下，数据库对故障的表现为：

- 当备库发生故障时，yasom会将主库的保护模式改为最大可用，防止业务阻塞，同时将禁止自动failover（因为最大可用模式存在数据丢失风险）。直到备库恢复正常，主备同步完成后，yasom将主库的保护模式恢复为最大保护并允许自动failover。

- 当主库发生故障时，yasom会检测备库是否连接超时，当时长超过FailoverThreshold参数值时，yasom将下发failover指令给备库，让其升主。

- 旧主库宕机后重启时，会与yasom确认角色，如果确认已有新的主库，旧主库将以备库角色启动。

<span id="failover_error_condition" name="failover_error_condition" class="yaslink"></span>
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
ALTER SYSTEM SET FAILOVER_HEALTH_CONDITION = 'INACCESSIBLE_LOGFILE, DATAFILE_WRITE_ERRORS' scope = both;

  -- 设置主库抛出数据库错误为311时，关闭主库并触发选举
ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311' scope = both;

  -- 设置主库抛出数据库错误为311并且对应的系统错误码为28时，关闭主库并触发选举
ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311-28' scope = both;
  ```

  >  **Note**：
  >
  >  仅当开启yasom仲裁选主时，条件故障切换参数相关配置会在数据库为主库时生效，并且不会自动同步给备库。

2. 一旦主库在运行中出现切换条件所指定的异常或错误，主库会尝试同步剩余redo日志给备库，然后立即关闭（终止YASDB进程）。

3. 备库将触发心跳超时，yasom下发failover指令给备库，让其升主。

## 常见问题

#### 如何查看yasom仲裁选主记录？

使用yasboot election event命令可以查看yasom仲裁选主的记录，包括failover，保护模式切换，脑裂修复，数据库启动角色确认等。

#### yasom仲裁选主启动失败该如何处理？

开启yasom仲裁选主功能的前提是主备库状态正常。若功能启动失败，请检查主备连接状态，以及每个数据库V$DATABASE的STATUS是否为NORMAL。如果数据库不正常，请通过查询V$DIAG_INCIDENT，查看告警日志，运行日志等定位，常见的故障可能有磁盘空间不足，网络故障等。

#### 为什么无法切换到最大保护模式？

切换到最大保护模式时要求主库的redo已完全同步给备库。如果备库故障或备库redo接收速度慢，都会导致redo无法同步，请检查备库状态是否正常，网络带宽是否充足，并保证足够的备库的硬件资源。

#### 备库Apply Lag越来越大该怎么处理？

备库回放速度慢，跟不上主库redo产生速度时，备库Apply Lag会越来越大。请保证备库拥有足够的硬件资源（不要远低于主库的资源），包括CPU、内存以及磁盘性能等。也可以尝试修改并行参数，提高并行度。

#### 主库故障后，长时间无法升主的可能原因有哪些？

- 可能是备库回放速度慢，有大量的redo等待回放。可以通过查询V$DATABASE的switchover_status确认备库是否在升主，如果是，请等待redo回放完毕。

- 备库状态异常，不可升主。检查是否出现need repair、是否磁盘空间满。

- 零丢失模式下，如果数据库处于最大可用模式是禁止自动failover的，如需强制failover，请强制关闭yasom仲裁后手动执行failover。

#### 强制关闭yasom仲裁选主后，主库重启报确认角色失败该怎么处理？

强制关闭yasom仲裁选主功能时会尝试重置每个数据库上的OM_ELECTION_ENABLE参数，如果数据库不可访问，会导致参数残留。该主库重启后，无法通过yasom确认角色，会启动失败。

请手动修改主备的OM_ELECTION_ENABLE参数。

#### 如何配置和管理某几个DN组的仲裁选主？

在yasboot election的命令中使用--group-ids可以指定DN组，不指定该参数选项表示对所有DN组生效。
