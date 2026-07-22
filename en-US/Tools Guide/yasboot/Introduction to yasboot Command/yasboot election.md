Election occurs in a primary-standby HA deployment scenario. When the primary node fails and is unable to serve, the system uses yasom arbitration to promote the standby node to primary and demote the original primary node to standby.

YashanDB supports enabling yasom arbitration in the following deployment modes, and it only takes effect when both yasom and the standby node's yasagent process are online. For specific configuration operations, please refer to [*yasom* Election](../../../High Availability/Configuring Leader Election/Configuring yasom Election).

- Standalone One-Primary/One-Standby Deployment

- Primary-Standby YAC Deployment

- ISC Distributed Cluster Deployment where nodes within the DN group are configured as one-primary/one-standby

- YAC Deployment one-primary/multi-standby

> **Caution**: 
>
> - The [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) must be enabled (default enabled during standard installation) for proper use of yasom election functionality.
>
> - Yasom arbitration and yasom self-repair are mutually exclusive. When the yasboot process is set to yasom repair on, yasom arbitration cannot be used.

## election enable on

This command is used to enable yasom-based election. Before enabling arbitration, you can configure the related parameters for arbitration using the [election config set](#set) command as needed.

|Option |Meaning |
| --------------- | -------------------- |
| *-c, --cluster*   | The name of the cluster deploying YashanDB (required) |
| *--group-ids*     | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

> **Note**: 
>
> - Before enabling, ensure that the primary/standby node statuses are normal and that the protection modes are the same.
> 
> - Enabling yasom election will modify the heartbeat interval and heartbeat timeout parameters of the database.

***Example***

```shell 
$ yasboot election enable on -c yashandb
```

## election enable off

This command is used to disable yasom election.

|Option |Meaning |
| ---------------- | -------------------------------- |
| *-c, --cluster*     | The name of the cluster deploying YashanDB (required) |
| *--group-ids*       | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-f, --force*       | Force disable, ignoring offline database nodes' parameter reset. If you do not use the --force option to disable arbitration, all nodes' election parameters will be reset, and all nodes must be online. |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

> **Note**: 
>
> - When some nodes are offline, you can use the --force option to ignore the parameter reset for offline nodes. You will need to manually reset the parameter OM_ELECTION_ENABLE to FALSE for the offline node; otherwise, when the database starts, it may fail to start due to an inability to confirm the role.
> - Disabling yasom election does not restore the heartbeat timeout and protection mode to the state before yasom election was enabled.

***Example***

```shell
$ yasboot election enable off -c yashandb
$ yasboot election enable off --force -c yashandb
```

## election status

This command is used to display the running status of yasom election.

|Option |Meaning |
| -------------------- | ------------------------------------- |
| *-c, --cluster*      | The name of the cluster deploying YashanDB (required) |
| *-node*      | The name of database node |
| *--group-ids*        | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Supports multiple, use commas to separate. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

***Example***

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




## election config set 

<span id="set" name="set"></span>

This command is used to set election-related parameters, and setting election parameters is only allowed when election is not enabled.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*   | The name of the cluster deploying YashanDB (required) |
| *-k, --key*       | The name of the parameter to set (required) |
| *-v, -value*      | The value corresponding to the parameter key to set (required) |
| *--group-ids*     | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-h,--help*          | View help information for the current command  |



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



***Example***

```shell
$ yasboot election config set -k FailoverThreshold -v 5 -c yashandb

# In YAC deployment, the FailoverTarget parameter needs to be configured with the group IDs
$ yasboot election config set -k FailoverTarget  -v "group 1:(2,3)"  -c yashandb
  
# In Standalone Deployment or ISC Distributed Cluster Deployment, the FailoverTarget parameter needs to be configured with the node IDs
$ yasboot election config set -k FailoverTarget  -v "node 1:(2,3)"  -c yashandb
```

## election config show

This command is used to display the election parameter settings, conditional switch configurations, and running status.

|Option |Meaning |
| -------------------- | ------------------------------------- |
| *-c, --cluster*      | The name of the cluster deploying YashanDB (required) |
| *--group-ids*        | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot election config show -c yashandb
```



The meanings of each field are shown in the following table.

|Field |Meaning |
| --------------------- |-----------------------------------------------------------|
| group n<br />cluster | Types of objects participating in *yasom* arbitration:<br />- `cluster`: Indicates that the current *yasom* arbitration is at the cluster level, i.e., the environment is in YAC Deployment<br />- `group n`: Indicates that the current *yasom* arbitration is at the node level, i.e., the environment is in Standalone Deployment or ISC Distributed Cluster Deployment, and `n` is the ID of the group to which the node belongs |
| Protection Mode | The protection mode of the primary node recorded by *yasom*. |
| Members | Information of nodes/groups participating in arbitration, including node status, node role, standby node transmission delay, replay delay, and replay rate, etc. |
| Database Error(s) | Record HA-related anomalies, such as the redo log of a standby database mismatching that of the primary database. |
| Properties | The currently effective parameter information. |
| Configurable Failover Conditions | Conditional failover configuration items for the primary database. Health Conditions correspond to the configuration parameter FAILOVER_HEALTH_CONDITION of the primary database, Error Code Conditions correspond to the configuration parameter FAILOVER_ERROR_CONDITION of the primary database, and only effective error codes are shown. For more details, please refer to [Configuring Conditional Failover](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby.md#failover_error_condition). |
| Automatic Failover | The status of *yasom* election<br />- DISABLED: *yasom* election is off.<br />- Enabled in Potential Data Loss Mode: normal mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode: zero loss mode *yasom* election is on.<br />- Enabled in Zero Data Loss Mode (NOT ALLOWED): zero loss mode *yasom*  election is on, but the database protection mode is not maximize protection, automatic switching is not allowed. |





## election config unset

This command is used to reset election-related parameters to their default values.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*   | The name of the cluster deploying YashanDB (required) |
| *-k, --key*       | The name of the parameter to set (required) |
| *--group-ids*     | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot election config unset -k FailoverThreshold -c yashandb
```

## election event show

This command is used to view election-related events, including event names, occurrence times, processing times, and whether processing was successful.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*   | The name of the cluster deploying YashanDB (required) |
| *--group-ids*     | Group ID (can be viewed via the `yasboot cluster status` command; the value before the dash in nodeid is the group-id, e.g., for `1-1:1`, the group ID is 1). Use commas to separate multiple IDs. This parameter does not take effect in Standalone Deployment; if not specified in ISC Distributed Cluster Deployment, it means all DN groups. |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot election event show -c yashandb

group 1
+--------------------------------------------------------------------------------------------------------------------------------------------+
| Name         | Node Id | Report Time         | Process Time        | Success | Ignore | Error                                              |
+--------------------------------------------------------------------------------------------------------------------------------------------+
| need repair  | 1-1:1   | 2023-06-14 16:35:35 | 2023-06-14 16:35:38 | Yes     | 0      |                                                    |
+--------------+---------+---------------------+---------------------+---------+--------+----------------------------------------------------+
| failover     | 1-2:2   | 2023-06-14 16:35:31 | 2023-06-14 16:35:32 | Yes     | 0      |                                                    |
+--------------+---------+---------------------+---------------------+---------+--------+----------------------------------------------------+
| confirm role | 1-2:2   | 2023-06-14 16:35:26 | 2023-06-14 16:35:26 | Yes     | 0      |                                                    |
+--------------+---------+---------------------+---------------------+---------+--------+----------------------------------------------------+
| failover     | 1-1:1   | 2023-06-14 16:35:22 | 2023-06-14 16:35:22 | Yes     | 0      |                                                    |
+--------------+---------+---------------------+---------------------+---------+--------+----------------------------------------------------+
| failover     | 1-2:2   | 2023-06-14 16:35:04 | 2023-06-14 16:35:05 | Yes     | 0      |                                                    |
+--------------+---------+---------------------+---------------------+---------+--------+----------------------------------------------------+
```

The events of Election are as follows:

|Event Name |Meaning |
| ------------------ | --------------------------- |
| failover            | When the standby node cannot connect to the primary node and heartbeat times out, it reports to yasom, notifying yasom to perform election. |
| confirm role        | When the primary node restarts, it needs to confirm its role. It reports to yasom to notify yasom to confirm the actual role of the node. If the old primary needs to be demoted to standby, it will directly start as a standby node. |
| need repair         | When the standby node is in NEED REPAIR state, it may be due to a split-brain condition in non-zero loss mode. The old primary cannot continue to receive log data after being demoted and needs to notify yasom to repair the standby node. |
| protection demote   | In zero loss mode, when the standby node is abnormal and causes the primary node's transactions to block, the primary notifies yasom to demote the protection mode to maximize availability mode and prohibit automatic switching. |
| protection promote   | In zero loss mode, when the standby node recovers synchronization, the primary notifies yasom to promote the protection mode back to maximize protection mode and restore automatic switching. |

> **Note**: 
>
> - A maximum of 5 entries can be saved for the same event. Older events will be discarded.
> - If yasom processes the need repair event more than 3 times within 1 minute and all fail, subsequent identical events will be ignored, and the ignored count will be added to the most recent event record. If the ignored event is not reported for more than a minute, the ignore state will be reset, and it will continue to process on the next occurrence.

## election target show

This command is used to view the configuration of the candidate standby database set for *yasom* election (the FailoverTarget parameter).

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*   | The cluster name used for the YAC deployment(required) |

***Example***

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

The `seted` field is used to indicate whether the configuration is manually configured:

- `seted = true` indicates that the configuration item is manually configured by the user.

- `seted = false` indicates that the value of the configuration item is the system default value.
