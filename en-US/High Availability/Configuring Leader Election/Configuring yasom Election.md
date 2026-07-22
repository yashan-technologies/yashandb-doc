## Functionality Introduction

The *yasom* election refers to the functionality of [election](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot election) based on the *yasom* process, which ensures business continuity. When the primary database is unable to provide external services due to an anomaly, the system upgrades the standby database to become the primary database through *yasom* arbitration, downgrading the original primary database to standby.

The *yasom* election takes effect only when the *yasom* and standby database's yasagent processes are online.

After enabling *yasom* Election, the *yasom* process monitors the database status. When the primary database fails, failover is executed at the standby database. When the standby database fails and the primary database's business is blocked under the maximum protection mode, the protection mode of the primary database is downgraded. Before the primary database starts, it connects to *yasom* or the standby database to confirm its actual role:

- If the connection fails, the primary database fails to start.

- If a new primary database is detected, the original primary database needs to be demoted to a standby, that is, the original primary database starts directly as a standby database.

The arbitration modes are divided into zero data loss mode and normal mode. The database's response to failures varies depending on the mode.

### Zero Data Loss Mode

After enabling zero-loss mode *yasom* arbitration failover, the primary-standby [protection mode](../Overview of YashanDB High Availability.md#protection_mode) supports maximum protection and maximum availability, and *yasom* automatically adjusts it to maximum protection.

- *yasom* continuously detects whether the standby connection to the primary times out. If the duration exceeds the FailoverThreshold parameter value and *yasom* is also disconnected from the primary, the primary is determined to be abnormal. At this point, the failover process is triggered, and *yasom* promotes the target standby to primary based on the priority of the candidate standby set (FailoverTarget parameter).

After the old primary restarts after a crash, it confirms the role with yasom. If it is confirmed that there is a new primary, the old primary starts as a standby.

- When a standby fails, *yasom* adjusts the primary's [synchronous standby configuration](../Defining Synchronous Standbys) (REQUIRED_SYNC_STANDBYS parameter), removing the failed standby from the synchronous standby list to avoid blocking primary transactions. If the standby recovers and completes primary-standby data synchronization, *yasom* restores the primary's synchronous standby configuration.

- When all standbys in the candidate standby set fail, *yasom* switches the primary's protection mode to maximum availability to avoid business blocking, while disabling automatic failover (maximum availability mode has data loss risk). Once any standby in FailoverTarget recovers and completes primary-standby data synchronization, *yasom* restores the primary's protection mode to maximum protection and re-enables automatic failover.

### Normal Mode

After enabling normal mode *yasom* arbitration failover, the primary-standby protection mode supports maximum availability and maximum performance. This mode prioritizes availability with fewer switching restrictions—as long as any standby is alive, failover can occur when the primary fails, but data loss is not guaranteed.

- *yasom* continuously detects whether the standby connection to the primary times out. If the duration exceeds the FailoverThreshold parameter value and *yasom* is also disconnected from the primary, the primary is determined to be abnormal. At this point, the failover process is triggered, and *yasom* promotes the target standby to primary based on the priority of the candidate standby set (FailoverTarget parameter).

- After the old primary restarts after a crash, it confirms the role with yasom. If it is confirmed that there is a new primary, the old primary starts as a standby.


<span id="Applicable" name="Applicable"></span>

## Applicable Scenarios

|Deployment Mode |High Availability Deployment Scale |
|--------------------|--------------------------------------|
| Standalone Deployment    | One-primary/one-standby deployment  |
| ISC Distributed Cluster Deployment    | One-primary/one-standby deployment within DN group  |
| YAC Deployment          | One-primary/one-standby cluster  <br/> One-primary/muti-standby cluster |

For unified terms, the primary database in an ISC Distributed Cluster Deployment environment refers to the primary node of the DN group, and the standby database refers to the standby node of the DN group. In a YAC Deployment environment, the primary database refers to the primary cluster, and the standby database refers to the standby cluster.

## Prerequisites

Ensure that [OS authentication](../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) functionality is enabled (it is enabled by default when following standard installation procedures).

## Configuration Steps

### Step 1: Prepare Environment

To deploy a HA environment, refer to the installation steps detailed in [Installation and Deployment](../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) or refer to [Scaling](../../Installation and Upgrade/Scalability/00Scalability) to build the corresponding environment. During environment preparation, please note:

- The number of standby databases must meet the requirements of [Applicable Scenarios](#Applicable).

- All resource parameters of the primary standby database should be consistent. The database version installed on the primary standby database must be the same.

- [OS authentication](../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) must be enabled (it is enabled by default when following standard installation procedures) to use the election functionality properly.

- It is recommended to deploy *yasom* on a separate server and not on the same server as the primary/standby databases.

> **Caution**: 
>
> - If the standby database is deployed on the same server as yasom, it is not advised to use election. If needed, it is recommended to use normal mode instead. Because in zero data loss mode, if both *yasom* and the standby database fail, the primary database's maximize protection mode cannot be downgraded, leading to business blocking on the primary database.
>
> - If *yasom* is unavailable, even in election zero data loss mode, the database protection mode cannot change (for example, if the standby database crashes, the primary database cannot change its protection mode from maximize protection to maximize availability), resulting in business blocking on the primary database.

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

# An example of a YAC one-primary/multi-standby environment:
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

### Step 2: Configure *yasom* Election Parameters



The parameters related to arbitration - based master selection are shown in the following table.

|Parameter Name |Default Value |Value Range/Format |Description |
| ----------------------- | ------ | ---------- | --------------------------- |
| FailoverThreshold          | 9             | [2, 1000]     | The heartbeat timeout for the standby node. After reaching this time, yasom will execute the failover process. |
| FailoverTarget          | - Primary: All standbys (excluding cascaded standbys)<br />- Standby: The primary | Format: group&#124;node n(x,y,z…) | Specify the target candidate group/node for standby-to-primary promotion and their priority order<br />\- group: Used to indicate that the numerical number in the subsequent configuration is the group ID, applicable to YAC Deployment. It can be viewed via the `yasboot cluster status` command. The value before the hyphen in the nodeId is the group ID<br />\- node: Used to indicate that the numerical number in the subsequent configuration is the node ID, applicable to Standalone Deployment or ISC Distributed Cluster Deployment. It can be viewed via the `yasboot cluster status` command. The value before the hyphen in the nodeId is the node ID<br />\- `n:(x,y,z…)`: Use the corresponding ID values to specify the specific target group/node and their priority order. The priority follows the order of configuration. If the parentheses are left empty `n:()`, it means that `n` will be removed from the default values of FailoverTarget for all groups/nodes.<br />For example, `node 1:(2,3)` means that the failover candidates for node 1 are node 2 and node 3, with node 2 having the first priority |
| FailoverAutoReinstate      | false         | true/false    | Whether to enable automatic split-brain recovery. <br/> If enabled, if the standby node experiences a split-brain and is in the NEED REPAIR state, yasom will attempt to automatically repair it. |
| ZeroDataLossMode           | true          | true/false    | Whether to enable zero loss mode. <br> If enabled, primary/standby will be set to maximize protection mode. When the primary node fails, the standby node can automatically failover; when the standby node is abnormal, the primary node will be downgraded by yasom to maximize availability mode, and automatic failover will be prohibited until the standby node synchronizes again, at which point yasom will upgrade the primary node back to maximize protection mode, allowing automatic failover. |

> **Caution**: 
>
> - A small FailoverThreshold may lead to unnecessary switches due to network jitter. Please set a reasonable timeout based on network conditions.
> - Enabling FailoverAutoReinstate will automatically repair the split-brain issue of the standby node, which may lead to some data loss where the standby node and primary node have discrepancies. Please **use with caution**.
> - Enabling ZeroDataLossMode prioritizes the use of maximize protection mode. In maximize protection mode, if the primary node fails, the standby node will automatically failover without data loss. When the standby node is abnormal, the primary node will be downgraded to maximize availability mode, at which point the standby node may risk data loss, therefore automatic failover will be disabled until the primary node again recovers maximize protection mode. Therefore, the conditional switch to zero loss mode is stricter but ensures no data is lost.



1. Execute the following commands to configure the *yasom* election parameters:

      ```shell
    # This is just an example value; please adjust according to actual needs
    $ yasboot election config set -k FailoverThreshold -v 5 -c yashandb
    $ yasboot election config set -k ZeroDataLossMode -v true -c yashandb

    # In YAC deployment, the FailoverTarget parameter needs to be configured with the group IDs
    $ yasboot election config set -k FailoverTarget  -v "group 1:(2,3)"  -c yashandb
    
    # In Standalone Deployment or ISC Distributed Cluster Deployment, the FailoverTarget parameter needs to be configured with the node IDs
    $ yasboot election config set -k FailoverTarget  -v "node 1:(2,3)"  -c yashandb
    ```

2. View the FailoverTarget configuration:
     
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

3. Check if the *yasom* election parameters are configured correctly and if the database status is normal:

    ```shell
    $ yasboot election config show -c yashandb
    ```

    Example of echoed information:

    ::: tabs
    == Standalone Deployment

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

    == YAC Deployment

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
    == ISC Distributed Cluster Deployment

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

    The meanings of each field are shown in the following table.

    |Field |Meaning |
    | --------------------- |-----------------------------------------------------------|
    | group n<br />cluster | Types of objects participating in *yasom* arbitration:<br />- `cluster`: Indicates that the current *yasom* arbitration is at the cluster level, i.e., the environment is in YAC Deployment<br />- `group n`: Indicates that the current *yasom* arbitration is at the node level, i.e., the environment is in Standalone Deployment or ISC Distributed Cluster Deployment, and `n` is the ID of the group to which the node belongs |
    | Protection Mode | The protection mode of the primary node recorded by *yasom*. |
    | Members | Information of nodes/groups participating in arbitration, including node status, node role, standby node transmission delay, replay delay, and replay rate, etc. |
    | Database Error(s) | Record HA-related anomalies, such as the redo log of a standby database mismatching that of the primary database. |
    | Properties | The currently effective parameter information. |
    | Configurable Failover Conditions | Conditional failover configuration items for the primary database. Health Conditions correspond to the configuration parameter FAILOVER_HEALTH_CONDITION of the primary database, Error Code Conditions correspond to the configuration parameter FAILOVER_ERROR_CONDITION of the primary database, and only effective error codes are shown. For more details, please refer to [Configuring Conditional Failover](#failover_error_condition). |
    | Automatic Failover | The status of *yasom* election<br />- DISABLED: *yasom* election is off.<br />- Enabled in Potential Data Loss Mode: normal mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode: zero loss mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode (NOT ALLOWED): zero loss mode *yasom*  election is on, but the database protection mode is not maximize protection, automatic switching is not allowed. |

    

Confirm whether the parameters related to *yasom* election have taken effect and that the primary/standby status is normal based on the returned information, and then proceed with subsequent operations.

### Step 3: Enable *yasom* Election

Before enabling *yasom* election, ensure that the statuses of both primary and standby databases are normal and that their protection modes are the same. In a YAC Deployment, also ensure that all instances are started, and each primary/standby cluster has at least one instance in the OPEN state.

1. Execute the following command to enable *yasom* election:

    ```shell 
    $ yasboot election enable on -c yashandb
    ```

2. Check the *yasom* election status:

    ```shell
    $ yasboot election status -c yashandb
    ```

    

The meanings of each field are shown in the following table.
    
|Field |Meaning |
| --------------------- |-----------------------------------------------------------|
| group n<br />cluster | Types of objects participating in *yasom* arbitration:<br />- `cluster`: Indicates that the current *yasom* arbitration is at the cluster level, i.e., the environment is in YAC Deployment<br />- `group n`: Indicates that the current *yasom* arbitration is at the node level, i.e., the environment is in Standalone Deployment or ISC Distributed Cluster Deployment, and `n` is the ID of the group to which the node belongs |
| Protection Mode | The protection mode of the primary node recorded by *yasom*. |
| Members | Information of nodes/groups participating in arbitration, including node status, node role, standby node transmission delay, replay delay, and replay rate, etc. |
| Database Error(s) | Record HA-related anomalies, such as the redo log of a standby database mismatching that of the primary database. |
| Automatic Failover | The status of *yasom* election<br />- DISABLED: *yasom* election is off.<br />- Enabled in Potential Data Loss Mode: normal mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode: zero loss mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode (NOT ALLOWED): zero loss mode *yasom*  election is on, but the database protection mode is not maximize protection, automatic switching is not allowed. |



"Enabled in Zero Data Loss Mode" indicates that *yasom* election has been enabled in zero data loss mode. The protection mode of primary/standby will be automatically changed to maximize protection, allowing for automatic failover (if the primary database fails, the standby database will automatically failover to assume primary).

<span id="failover_error_condition" name="failover_error_condition"></span>

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
    ALTER SYSTEM SET FAILOVER_HEALTH_CONDITION = 'INACCESSIBLE_LOGFILE, DATAFILE_WRITE_ERRORS' SCOPE = BOTH;

    -- Set to close the primary database and trigger election if it throws database error code 311
    ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311' SCOPE = BOTH;

    -- Set to close the primary database and trigger election if it throws database error code 311 and corresponding system error code 28
    ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311-28' SCOPE = BOTH;
    ```

    >  **Note**: 
    >
    >  Conditional failover parameter configurations will only take effect when *yasom* election is enabled, and will not be automatically synchronized to the standby database when the database is in the primary role.

2. Once the primary database encounters the specified exceptions or errors while running, it will attempt to synchronize the remaining redo logs to the standby database and then shut down immediately (terminating the YASDB process).

3. The standby database will trigger a heartbeat timeout, and *yasom* will issue a failover command to the standby database, allowing it to assume the primary status.

## Frequently Asked Questions

#### How to Plan Performance Requirements for Failover Target Standby Databases?

It is recommended to use a directly connected physical standby database with good network, excellent performance, and in the same region as the primary database as the Failover Target. In maximum protection and maximum availability modes, this standby database will be prioritized as a synchronous standby database. A remote or high-latency standby database may cause performance degradation of the primary database.

#### How to Remove Remote or High-Latency Standby Databases?

If FailoverTarget is not configured, it will be configured with the default value when yasom arbitration failover is enabled. To exclude remote or high-latency standbys from the default value, you can use group group_id:() or node node_id:() to set the candidate standby set for the corresponding node to an empty set. After the configuration takes effect, the node will be removed from the default FailoverTarget of all nodes.

```shell
# In YAC Deployment, the group ID of a standby cluster must be used when removing it
$ yasboot election config set -k FailoverTarget  -v "group 3:()"  -c yashandb

# In Standalone Deployment or ISC Distributed Cluster Deployment, the node ID of a standby database/node must be used when removing it
$ yasboot election config set -k FailoverTarget  -v "node 3:()"  -c yashandb
```

#### How can I view *yasom* election records?

You can view *yasom* election records, including failover, protection mode switching, split-brain repairs, and database role confirmation, by using the `yasboot election event` command.

#### What should I do if the *yasom* election fails to start?

The prerequisite for enabling the arbitration election functionality is that the primary and standby database statuses are normal. If the functionality fails to start, please check the primary/standby connection status and whether the STATUS of each database in v$database is NORMAL. If the database is abnormal, please query v$diag_incident to examine the alert log, runtime log, etc., to locate the issue; common faults may include insufficient disk space, network failures, etc.

#### Why can't I switch to maximize protection mode?

Switching to maximize protection mode requires that the redo of the primary database has been fully synchronized with the standby database. If the standby database is down or the redo reception speed of the standby database is slow, it may lead to unsynchronized redo; please check whether the standby database status is normal, whether network bandwidth is sufficient, and ensure adequate hardware resources for the standby database.

#### What should I do if the standby database apply lag is increasing?

If the standby database's apply speed cannot keep up with the primary database's redo generation speed, the standby database apply lag will increase. Please ensure that the standby database has sufficient hardware resources (not significantly lower than those of the primary database), including CPU, memory, and disk performance, etc. You may also try adjusting parallel parameters to increase parallelism.

#### What could cause the primary database to fail and not be able to assume primary status for a long time?

- It may be that the standby database's apply speed is slow, with a large amount of redo waiting to be applied. You can check if the standby database is in the process of assuming primary status by querying the switchover_status in v$database; if so, please wait for the redo apply to complete.

- The *yasom* process may be abnormal, for example, if the *yasom* process is shut down, the server where the primary database is located is completely down, or the primary-standby configuration of the *yasom* process is incorrect. In this case, [repair *yasom* faults](../../Tools Guide/yasboot/Manage Yasom/Scenario Fault Handling for yasom) according to the situation.

- The standby database may be in an abnormal state and cannot assume primary status. Check if it is in a need repair state or if the disk space is full.

- In zero data loss mode, if the database is in maximize availability mode, automatic failover is prohibited. If you need to force a failover, please forcibly disable *yasom* arbitration and manually perform the failover.

#### What should I do if the primary database fails to confirm role upon restart after forcibly disabling *yasom* election?

When forcibly disabling the *yasom* election functionality, it attempts to reset the OM_ELECTION_ENABLE parameter on each database. If the database becomes inaccessible, it will lead to residual parameters. When this primary database restarts, it cannot confirm its role via *yasom* and will fail to start.

Please manually modify the OM_ELECTION_ENABLE parameters for the primary/standby databases.

#### In an ISC Distributed Cluster Deployment, how do I configure and manage the election for certain DN groups?

Use the --group-ids option in the yasboot election command to specify DN groups; omitting this parameter applies to all DN groups.

#### Why doesn't the standby database automatically assume primary status after shutting down the primary database with the yasboot stop command, but does after using the SQL statement to shut down the primary database?

The *yasom* views commands issued through *yasboot* such as stop, restart, etc., as user-initiated operational actions rather than fault scenarios; therefore, it does not issue a failover command to promote primary. If you directly shut down the database with a SQL statement, *yasom* cannot perceive it, and will regard it as a fault scenario, thus triggering the leader election.

When *yasom* election functionality is enabled, it is recommended to use *yasboot* commands for operational actions such as node startup and shutdown to avoid unnecessary failovers.
