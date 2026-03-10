## Functionality Introduction

The yasom election refers to the functionality of [election](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot election) based on the yasom process, which ensures business continuity. When the primary database is unable to provide external services due to an anomaly, the system upgrades the standby database to become the primary database through yasom arbitration, downgrading the original primary database to standby.

The yasom election takes effect only when the yasom and standby database's yasagent processes are online.

After activating yasom election, the primary database will connect to yasom or the standby database to confirm its actual role before startup; if the connection fails, the startup of the primary database will fail. If a downgrade is needed, the original primary database will start directly as a standby database. During normal operation, the yasom process monitors the database status; when the primary database fails, failover is executed on the standby database. If the standby database fails under maximize protection mode and the primary database is blocked, the protection mode of the primary database will be downgraded.

The arbitration modes are divided into zero data loss mode and normal mode, which are as follows:

- Zero Data Loss Mode (ZeroDataLossMode=true): The primary/standby [protection mode](../YashanDB高可用概述.html#protection_mode) supports maximize protection and maximize availability. After enabling zero data loss mode yasom election, yasom will automatically adjust the protection mode to maximize protection. In a maximize protection scenario, if the primary database fails, yasom will automatically perform a failover switch. If a certain standby database is abnormal, the primary database will be downgraded to maximize availability mode. If the primary database fails at this time, to ensure the standby database does not lose data (i.e., RPO=0), yasom will not automatically failover.

- Normal Mode (ZeroDataLossMode=false): The primary/standby protection mode supports maximize availability and maximize performance. There are fewer restrictions on switching but does not guarantee RPO=0, which may lead to data loss.

## Applicable Scenarios

|Deployment Mode |High Availability Deployment Scale |
|--------------------|--------------------------------------|
| Standalone Deployment    | One-primary/one-standby deployment  |
| ISC Distributed Cluster Deployment    | One-primary/one-standby deployment within DN group  |
| YAC Deployment          | One main cluster, one standby cluster |

For unified terms, the primary database in an ISC Distributed Cluster Deployment environment refers to the main node of the DN group, and the standby database refers to the standby node of the DN group. In a YAC Deployment environment, the primary database refers to the main cluster, and the standby database refers to the standby cluster.

## Prerequisites

Ensure that [OS authentication](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) functionality is enabled (it is enabled by default when following standard installation procedures).

## Configuration Steps

### Step 1: Prepare One-Primary/One-Standby Environment

To deploy a one-primary/one-standby high availability environment, refer to the installation steps detailed in [Installation and Deployment](../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) or refer to [Scaling](../../Installation and Upgrade/Scalability/00Scalability) to build the corresponding environment. During environment preparation, please note:

- The number of standby databases is limited to 1.

- All resource parameters of the primary standby database should be consistent. The database version installed on the primary standby database must be the same.

- [OS authentication](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) must be enabled (it is enabled by default when following standard installation procedures) to use the election functionality properly.

- It is recommended to deploy yasom on a separate server and not on the same server as the primary/standby databases.

> **Caution**: 
>
> - If the standby database is deployed on the same server as yasom, it is not advised to use election. If needed, it is recommended to use normal mode instead. Because in zero data loss mode, if both yasom and the standby database fail, the primary database's maximize protection mode cannot be downgraded, leading to business blocking on the primary database.
>
> - If yasom is unavailable, even in election zero data loss mode, the database protection mode cannot change (for example, if the standby database crashes, the primary database cannot change its protection mode from maximize protection to maximize availability), resulting in business blocking on the primary database.

Once the environment is prepared, you can check whether the database deployment scale meets the requirements:

```shell
$ yasboot cluster status -c yashandb -d

# A standalone one-primary/one-standby environment will display as follows:
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+

# A distributed DN group one-primary/one-standby environment will display as follows:
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

# An example of a YAC one-primary/one-standby environment:
+-------------------------------------------------------------------------------------------------------------------------------------------------+
+ hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address     | data_path                      |
|-------------------------------------------------------------------------------------------------------------------------------------------------+
+ host0001 | ce        | 1-1:1  | 19513 | open            | normal          | primary       | 192.168.1.2:1688   | /data/yashan/yasdb_data/ce-1-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0002 | ce        | 1-2:2  | 19608 | open            | normal          | primary       | 192.168.1.3:1688   | /data/yashan/yasdb_data/ce-1-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0003 | ce        | 2-1:3  | 19703 | open            | normal          | standby       | 192.168.1.4:1688   | /data/yashan/yasdb_data/ce-2-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0004 | ce        | 2-2:4  | 19651 | open            | normal          | standby       | 192.168.1.5:1688   | /data/yashan/yasdb_data/ce-2-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
```

### Step 2: Configure *yasom* Election Parameters

The relevant parameters for yasom Election are as follows:

|Parameter Name |Default Value |Value Range |Parameter Description |
| ----------------------- | ------ | ---------- | --------------------------- |
| FailoverThreshold            | 9             | [2,1000]   | The heartbeat timeout for the standby database to connect to the primary database (unit: seconds); if this time is reached, yasom will execute the failover switch process. |
| FailoverAutoReinstate       | false         | [true&#124;false] | Whether to enable automatic split-brain repair. <br/> If enabled, yasom will attempt to automatically repair if the standby database experiences a split-brain and is in NEED REPAIR state. This option is not supported in YAC mode. |
| ZeroDataLossMode            | true          | [true&#124;false] | Whether to enable zero loss mode. <br/> If enabled, the primary/standby will automatically set to maximize protection mode. <br/> * If the primary database crashes, the standby database can automatically failover. <br/> * If the standby database is abnormal, yasom will downgrade the primary database's protection mode to maximize availability and prohibit automatic failover until the standby database resumes synchronization, at which point yasom will restore the primary database to maximize protection mode and allow automatic failover. |

1. Execute the following commands to configure the *yasom* election parameters:

    ```shell
    # This is just an example value; please adjust according to actual needs
    $ yasboot election config set -k FailoverThreshold -v 5 -c yashandb
    $ yasboot election config set -k ZeroDataLossMode -v true -c yashandb
    ```

2. Check if the yasom election parameters are configured correctly and if the database status is normal:

    ```shell
    $ yasboot election config show -c yashandb

    # The standalone one-primary/one-standby environment will display as follows:
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

    # The distributed DN group one-primary/one-standby environment will display as follows:
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

    # YAC one-primary/one-standby environment will display as follows:
    cluster
      Protection Mode: MAXIMUM AVAILABILITY
      Members:
        [1-1:1] - Primary database
        [1-2:2] - Primary database
          [2-1:3] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     0 seconds
                      Apply Rate:    0.00 KByte/s
          [2-2:4] - Physical standby database

      Properties:
        FailoverThreshold      = 5
        FailoverAutoReinstate  = false
        ZeroDataLossMode       = true

    Automatic Failover: DISABLED
    ```

Based on the return information, the yasom election-related parameters have taken effect, and the primary/standby status is normal with no alerts displayed.

### Step 3: Enable *yasom* Election

Before enabling yasom election, ensure that the statuses of both primary and standby databases are normal and that their protection modes are the same. In a YAC Deployment, also ensure that all instances are started, and each primary/standby cluster has at least one instance in the OPEN state.

1. Execute the following command to enable yasom election:

    ```shell 
    $ yasboot election enable on -c yashandb
    ```

2. Check the yasom election status:

    ```shell
    $ yasboot election status -c yashandb

    # The standalone one-primary/one-standby environment will display as follows:
    group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     15 seconds
                      Apply Rate:    0.00 KByte/s

    Automatic Failover: Enabled in Zero Data Loss Mode

    # The distributed DN group one-primary/one-standby environment will display as follows:
    group 3
    Protection Mode: MAXIMUM PROTECTION
    Members:
      [3-1:7] - Primary database
        [3-2:8] - Physical standby database
                    Transport Lag: 0 seconds
                    Apply Lag:     15 seconds
                    Apply Rate:    0.00 KByte/s

    Automatic Failover: Enabled in Zero Data Loss Mode

    # YAC one-primary/one-standby environment will display as follows:
    cluster
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
        [1-2:2] - Primary database
          [2-1:3] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     0 seconds
                      Apply Rate:    0.00 KByte/s
          [2-2:4] - Physical standby database

      Properties:
        FailoverThreshold      = 5
        FailoverAutoReinstate  = false
        ZeroDataLossMode       = true

    Automatic Failover: Enabled in Zero Data Loss Mode
    ```

    According to the returned information, "Enabled in Zero Data Loss Mode" indicates that yasom election has been enabled in zero data loss mode. The protection mode of primary/standby will be automatically changed to maximize protection, allowing for automatic failover (if the primary database fails, the standby database will automatically failover to assume primary).

In the current configuration, the database response to faults is as follows:

- When the standby database fails, yasom will change the protection mode of the primary database to maximize availability to prevent business blocking, while prohibiting automatic failover (because maximize availability mode carries the risk of data loss). Until the standby database is operational again and synchronization is complete, yasom will restore the primary database's protection mode to maximize protection and allow automatic failover.

- When the primary database fails, yasom will check if the standby database's connection to the primary database has timed out; when the duration exceeds the FailoverThreshold parameter value, yasom will issue a failover command to the standby database, allowing it to assume primary status.

- When the old primary database restarts after crashing, it will confirm its role with yasom. If it confirms that a new primary database has already been established, the old primary database will start as a standby database.

<span id="failover_error_condition" name="failover_error_condition" class="yaslink"></span>

### Step 4 (Optional): Configure Conditional Failover

Conditional failover is an optional configuration that can be selected based on actual needs.

In certain scenarios, to prevent the primary database from being unable to continue providing services due to specific exceptions (while the network between primary and standby remains normal), you can proactively specify conditions for automatic switch to trigger a leader election, thus ensuring continuous service from the database. There are two types of conditional failover, controlled by the following two parameters:

- FAILOVER_ERROR_CONDITION: This can be configured so that when the primary database throws certain error codes to the client, the primary database immediately shuts down. Error codes can be a combination of database error codes and system error codes. It is recommended to avoid choosing high-frequency error codes to prevent frequent triggering of automatic switches. For file-related operation exceptions, the database error code may be the same but the system error code may differ, thus it is recommended to use the format "database error code-system error code" to specify the triggering conditions more accurately.

- FAILOVER_HEALTH_CONDITION: This can be configured so that when the primary database experiences certain abnormal scenarios, it immediately shuts down. This includes scenarios such as archive disk being full, redo write failures, dbwr dirty page write failures, etc.
  * CORRUPTED_CONTROLFILE: Indicates that the primary database failed to write the control file.

  * INACCESSIBLE_LOGFILE: Indicates that the primary database failed to write the redo file.

  * STUCK_ARCHIVER: Indicates that the primary database encountered archive failures due to a full disk.
    
  * DATAFILE_WRITE_ERRORS: Indicates that the primary database encountered dirty page write failures.

  * DATAFILE_NO_SPACE: Indicates that the primary database failed to create or write a data file due to a full disk.

  * SLICE_NO_SPACE: Indicates that the primary database failed to create or write a slice file due to a full disk.

The process for conditional failover is as follows:

1. Configure the automatic switching conditions as needed.

  ```sql
  -- This is merely an example; please configure according to actual needs
  -- Set to close the primary database and trigger an election if redo write fails or if dbwr dirty page write fails
ALTER SYSTEM SET FAILOVER_HEALTH_CONDITION = 'INACCESSIBLE_LOGFILE, DATAFILE_WRITE_ERRORS' scope = both;

  -- Set to close the primary database and trigger election if it throws database error code 311
ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311' scope = both;

  -- Set to close the primary database and trigger election if it throws database error code 311 and corresponding system error code 28
ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311-28' scope = both;
  ```

  >  **Note**: 
  >
  >  Conditional failover parameter configurations will only take effect when yasom election is enabled, and will not be automatically synchronized to the standby database when the database is in the primary role.

2. Once the primary database encounters the specified exceptions or errors while running, it will attempt to synchronize the remaining redo logs to the standby database and then shut down immediately (terminating the YASDB process).

3. The standby database will trigger a heartbeat timeout, and yasom will issue a failover command to the standby database, allowing it to assume the primary status.

## Frequently Asked Questions

#### How can I view yasom election records?

You can view yasom election records, including failover, protection mode switching, split-brain repairs, and database role confirmation, by using the `yasboot election event` command.

#### What should I do if the yasom election fails to start?

The prerequisite for enabling the arbitration election functionality is that the primary and standby database statuses are normal. If the functionality fails to start, please check the primary/standby connection status and whether the STATUS of each database in V$DATABASE is NORMAL. If the database is abnormal, please query V$DIAG_INCIDENT to examine the alert log, runtime log, etc., to locate the issue; common faults may include insufficient disk space, network failures, etc.

#### Why can't I switch to maximize protection mode?

Switching to maximize protection mode requires that the redo of the primary database has been fully synchronized with the standby database. If the standby database is down or the redo reception speed of the standby database is slow, it may lead to unsynchronized redo; please check whether the standby database status is normal, whether network bandwidth is sufficient, and ensure adequate hardware resources for the standby database.

#### What should I do if the standby database apply lag is increasing?

If the standby database's apply speed cannot keep up with the primary database's redo generation speed, the standby database apply lag will increase. Please ensure that the standby database has sufficient hardware resources (not significantly lower than those of the primary database), including CPU, memory, and disk performance, etc. You may also try adjusting parallel parameters to increase parallelism.

#### What could cause the primary database to fail and not be able to assume primary status for a long time?

- It may be that the standby database's apply speed is slow, with a large amount of redo waiting to be applied. You can check if the standby database is in the process of assuming primary status by querying the switchover_status in V$DATABASE; if so, please wait for the redo apply to complete.

- The yasom process may be abnormal, for example, if the yasom process is shut down, the server where the primary database is located is completely down, or the primary-standby configuration of the yasom process is incorrect. In this case, [repair yasom faults](../../工具手册/yasboot/管理yasom/yasom常见故障处理) according to the situation.

- The standby database may be in an abnormal state and cannot assume primary status. Check if it is in a need repair state or if the disk space is full.

- In zero data loss mode, if the database is in maximize availability mode, automatic failover is prohibited. If you need to force a failover, please forcibly disable yasom arbitration and manually perform the failover.

#### What should I do if the primary database fails to confirm role upon restart after forcibly disabling yasom election?

When forcibly disabling the yasom election functionality, it attempts to reset the OM_ELECTION_ENABLE parameter on each database. If the database becomes inaccessible, it will lead to residual parameters. When this primary database restarts, it cannot confirm its role via yasom and will fail to start.

Please manually modify the OM_ELECTION_ENABLE parameters for the primary/standby databases.

#### In an ISC Distributed Cluster Deployment, how do I configure and manage the election for certain DN groups?

Use the --group-ids option in the yasboot election command to specify DN groups; omitting this parameter applies to all DN groups.

#### Why doesn't the standby database automatically assume primary status after shutting down the primary database with the yasboot stop command, but does after using the SQL statement to shut down the primary database?

The yasom views commands issued through *yasboot* such as stop, restart, etc., as user-initiated operational actions rather than fault scenarios; therefore, it does not issue a failover command to promote primary. If you directly shut down the database with a SQL statement, yasom cannot perceive it, and will regard it as a fault scenario, thus triggering the leader election.

When yasom election functionality is enabled, it is recommended to use *yasboot* commands for operational actions such as node startup and shutdown to avoid unnecessary failovers.