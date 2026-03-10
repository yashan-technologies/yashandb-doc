## Functionality Introduction

Primary/standby leader election refers to the mechanism based on the Raft election algorithm (three or more nodes forming a majority) to ensure business continuity. When the primary database fails and cannot provide service, the system elects a node from multiple standby database nodes to become the new primary database (with the others remaining as standby databases), and the old primary database is demoted to standby database.

The leader election configuration mainly involves the following parameters:

- NODE_ID: The globally unique ID for each primary/standby node, which is automatically configured during deployment/scaling and cannot be modified.

- HA_ELECTION_TIMEOUT: The heartbeat timeout, based on which the status of database nodes is assessed.

- HA_HEARTBEAT_INTERVAL: The interval for sending heartbeats; the primary database periodically sends heartbeats to all followers (standby databases) to maintain its position.

- HA_ELECTION_ENABLED: The main switch for leader election functionality.

- HA_ELECTION_LEADER_LEASE_ENABLED: Whether to actively demote when the primary database has not received heartbeat responses from the majority standby databases; optional configuration.

- HA_ELECTION_PRIORITY: Node priority; after enabling leader election, nodes with higher priority are more likely to become the new primary while ensuring data safety; optional configuration.

- HA_ELECTION_AUTO_PRIMARY_SWITCH: Whether to switch primary/standby when the primary database detects a higher priority standby database; optional configuration.

## Applicable Scenarios

|Deployment Type |High Availability Deployment Scale |
|--------------------|--------------------------------------|
| Standalone Deployment   | One-primary/multi-standby deployment with **no cascade standby** |
| ISC Distributed Cluster Deployment   | One-primary/multi-standby deployment within DN groups |

## Enable Leader Election

### Step 1: Prepare One-Primary/Multi-Standby Environment

Deploy a Standalone Deployment one-primary/one-standby high availability environment. Refer to the installation steps described in [Installation and Deployment](../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) for the Standalone Deployment (primary/standby) deployment, or refer to [Scalability](../../Installation and Upgrade/Scalability/00Scalability) for building the relevant environment. During the environment preparation, be aware of the following:

- There must be at least two standby databases, and cascade standby must not be added.

- The database versions installed on the primary and standby databases must be consistent, and it is recommended that resource parameters are kept consistent.

- The DATA directory defined during the installation of the primary and standby databases should be as consistent as possible.

After preparing the environment, check whether the database deployment scale meets the requirements:

  ```shell
  $ yasboot cluster status -c yashandb -d

# Example of a standalone one-primary/multi-standby environment:
  +-----------------------------------------------------------------------------------------------------------------------------------------------+
  | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
  +-----------------------------------------------------------------------------------------------------------------------------------------------+
  | host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
  | host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
  | host0003 | db        | 1-3:3  | 49582 | open            | normal          | standby       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+

# Example of a distributed DN node one-primary/multi-standby environment:
  +-------------------------------------------------------------------------------------------------------------------------------------------------+  
  | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | data_path                       |
  +-------------------------------------------------------------------------------------------------------------------------------------------------+
  | host0001 | mn        | 1-1:1  | 56961 | open            | normal          | primary       | 192.168.1.2:1678  | /data/yashan/yasdb_data/mn-1-1  |
  |          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  |          | dn        | 3-2:7  | 57029 | open            | normal          | standby       | 192.168.1.2:1698  | /data/yashan/yasdb_data/dn-3-2  |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  | host0002 | mn        | 1-2:2  | 57024 | open            | normal          | standby       | 192.168.1.3:1678  | /data/yashan/yasdb_data/mn-1-2  |
  |          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  |          | cn        | 2-1:4  | 57024 | open            | normal          | primary       | 192.168.1.3:1688  | /data/yashan/yasdb_data/cn-2-1  |
  |          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  |          | dn        | 3-3:8  | 57016 | open            | normal          | standby       | 192.168.1.3:1698  | /data/yashan/yasdb_data/dn-3-3  |
  +----------+-------+-----------------+-----------------+---------------+--------------------+-------------------+---------------------------------+
  | host0003 | mn        | 1-3:3  | 57021 | open            | normal          | standby       | 192.168.1.4:1678  | /data/yashan/yasdb_data/mn-1-3  |
  |          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  |          | cn        | 2-2:5  | 57021 | open            | normal          | primary       | 192.168.1.4:1688  | /data/yashan/yasdb_data/cn-2-2  |
  |          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  |          | dn        | 3-1:6  | 57021 | open            | normal          | primary       | 192.168.1.4:1698  | /data/yashan/yasdb_data/dn-3-1  |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
  ```

### Step 2: Set Heartbeat Time

The leader election functionality relies on the following two time parameters, which are typically configured to their default values:

- HA_ELECTION_TIMEOUT: Heartbeat timeout, in seconds, with a default value of 9.

- HA_HEARTBEAT_INTERVAL: Heartbeat sending interval, in seconds, with a default value of 3.

If the standby database does not receive a heartbeat after the HA_ELECTION_TIMEOUT setting elapses, it will automatically initiate an election.

>  **Caution**: 
>
> The heartbeat interval and heartbeat timeout configurations for primary and standby databases/nodes involved in leader election must be consistent; otherwise, it may lead to false heartbeat timeout judgments and cause erroneous switches.

```sql
-- Check heartbeat timeout
SHOW PARAMETER HA_ELECTION_TIMEOUT
NAME                      VALUE     
------------------------- --------- 
HA_ELECTION_TIMEOUT       9                                                                   

-- Check heartbeat sending interval
SHOW PARAMETER HA_HEARTBEAT_INTERVAL
NAME                      VALUE     
------------------------- --------- 
HA_HEARTBEAT_INTERVAL     3           
```

### Step 3: Adjust Primary/Standby Databases Protection Mode

To ensure that no committed transactions are lost in the newly elected primary after any node failure, it is recommended to adjust the protection modes of both the primary and standby databases to the MAXIMUM PROTECTION mode.

>**Caution**:
>
> If other protection modes are set and a failover occurs, it may lead to data loss and split-brain situations.

1. Query the V$DATABASE view sequentially on the primary and standby databases to confirm the current protection mode through the protection_mode field:

    ```sql
    SELECT database_id,database_name,open_mode,database_role,protection_mode FROM V$DATABASE;
    ```

    If the protection mode is MAXIMUM PROTECTION, no adjustment is necessary; otherwise, it is recommended to adjust to the MAXIMUM PROTECTION mode.

2. To switch the protection mode, execute the following statement on the primary database:

    ```sql
    ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION;
    ```

    The protection mode of the primary database will synchronize to the standby database with the heartbeat (this may take one heartbeat interval). To expedite the protection mode setting, after changing the protection mode on the primary database, do the same on the standby database.

3. Check the protection mode of the primary and standby databases again to verify if the configuration has taken effect:

    ```sql
    SELECT database_id,database_name,open_mode,database_role,protection_mode FROM V$DATABASE;
    ```

<span id="leader_lease" name="leader_lease" class="yaslink"></span>

### Step 4: Enable Primary/Standby Databases Leader Election

The leader election switch is controlled by the HA_ELECTION_ENABLED parameter, with a default value of FALSE, which means the leader election functionality is disabled. To enable leader election functionality, set this parameter to TRUE.

When enabling leader election, follow the operation sequence of **primary first, standby later**.

The HA_ELECTION_ENABLED parameter can be modified online. Execute the following statements in the primary database and all standby databases to check and modify:

```sql
-- Check the value of parameter HA_ELECTION_ENABLED
SHOW PARAMETER HA_ELECTION_ENABLED

-- Modify the value of parameter HA_ELECTION_ENABLED
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
```

>  **Caution**: 
>
>- When the Raft cluster is started, if leader election needs to be enabled, the order must be **start leader election for the primary database first, then enable leader election for standby databases**; otherwise, it may easily lead to the standby database automatically promoting to primary, resulting in a dual-primary situation.
>
>- When the leader election functionality is enabled, shutting down the primary database may trigger the standby database to automatically promote to primary. To avoid this, manually disable leader election before executing shutdown, following the order of **disable standby database leader election first, then shut down primary database leader election**.

### Step 5: Configure Automatic Demotion

Automatic demotion controls whether to actively demote the primary database when it fails to receive heartbeat responses from the majority of standby databases; the switch for automatic demotion is controlled by the HA_ELECTION_LEADER_LEASE_ENABLED parameter, with a default value of FALSE, indicating it is not enabled.

The HA_ELECTION_LEADER_LEASE_ENABLED parameter can be modified online, and it is effective only for the primary database. Users can configure it as needed based on their business system requirements.

When this parameter is set to TRUE, the primary database will automatically demote itself after the heartbeat timeout (HA_ELECTION_TIMEOUT) in the following situations:

- In maximize protection mode, if the primary database receives less than `QUORUM_SYNC_STANDBY syncNum` heartbeats from standby databases, where syncNum is the user-defined number of synchronous standbys.

- In non-maximize protection mode, if the primary database receives less than `(N / 2 + 1)` heartbeats from standby databases, where N is the total number of databases (including the primary database); if the result is a decimal, it is rounded down directly.

```sql
-- Check if automatic demotion functionality is enabled for primary database
SHOW PARAMETER HA_ELECTION_LEADER_LEASE_ENABLED;
NAME                               VALUE     
---------------------------------  --------- 
HA_ELECTION_LEADER_LEASE_ENABLED   FALSE                                                                             

-- Enable automatic demotion functionality for primary database
ALTER SYSTEM SET HA_ELECTION_LEADER_LEASE_ENABLED=TRUE;
```

>**Caution**:
>
> In a Standalone Deployment one-primary/multi-standby deployment, before performing recovery operations on the primary database (especially when only the isolated primary database remains in a failure scenario), it is necessary to disable the automatic demotion functionality to avoid triggering demotion due to the failure to detect standby database heartbeats in a timely manner. Restore the configuration as needed after recovery is complete.

### Step 6: Configure Node Priority

Node priority is configured through the HA_ELECTION_PRIORITY parameter; a higher value indicates a higher priority for that node, with the default value set to 1.

After enabling leader election, nodes with higher priority are more likely to become the new primary while ensuring data safety. When a node's priority is set to 0, that node will not initiate an election and will not become the next primary through the leader election mechanism.

The HA_ELECTION_PRIORITY parameter can be modified online, and users can configure it as needed based on their business system requirements.

```sql
-- Check the priority value of the target node
SHOW PARAMETER HA_ELECTION_PRIORITY;
NAME                               VALUE     
---------------------------------  --------- 
HA_ELECTION_PRIORITY               1                                                                             

-- Modify the priority value of the target node
ALTER SYSTEM SET HA_ELECTION_PRIORITY=2;
```

### Step 7: Configure Automatic Primary Switching

Automatic primary switching controls whether the primary database will switch to standby when it discovers a higher priority standby database. The switch is configured through the HA_ELECTION_AUTO_PRIMARY_SWITCH parameter, with a default value of FALSE, indicating it is not enabled.

This parameter supports online modification and is effective only for the primary database. Users can configure it as needed based on their business system requirements.

When this parameter is set to TRUE, the primary database will check for any higher priority nodes at regular intervals, and if found, it will switch primary/standby. The default interval is 60 seconds; if you need to adjust this interval, please contact our technical support.

```sql
-- Check if automatic primary switching functionality is enabled for primary database
SHOW PARAMETER HA_ELECTION_AUTO_PRIMARY_SWITCH;
NAME                                      VALUE     
------------------------------------  --------- 
HA_ELECTION_AUTO_PRIMARY_SWITCH           FALSE                                                                             

-- Enable automatic primary switching functionality for primary database
ALTER SYSTEM SET HA_ELECTION_AUTO_PRIMARY_SWITCH=TRUE;
```

<span id="failover_error_condition" name="failover_error_condition" class="yaslink"></span>

### Step 8 (Optional): Configure Conditional Failover

Conditional failover is an optional configuration and can be specified based on actual needs.

In certain scenarios, to prevent the primary database from potentially becoming unable to provide service due to specific exceptions (while the network between primary and standby remains normal), you can specify conditions for automatic switching in advance to timely trigger leader election, thereby ensuring the database continues to provide service. There are two types of conditional failover, controlled by the following two parameters:

- FAILOVER_ERROR_CONDITION: This can be configured such that when the primary database throws certain error codes to the client, the primary database will immediately shut down. The error codes can be database error codes or a combination of database error codes plus system error codes. It is recommended to avoid selecting frequently occurring error codes to prevent excessive triggering of automatic switches. For exceptions related to file operations, the database error codes may be the same, but the system error codes may differ, so it is recommended to specify trigger conditions more accurately using the "database error code-system error code" format.

- FAILOVER_HEALTH_CONDITION: This can be configured such that the primary database will immediately close under the following exception scenarios:
  * CORRUPTED_CONTROLFILE: Indicates failure to write the control file for the primary database.

  * INACCESSIBLE_LOGFILE: Indicates failure to write the redo file for the primary database.

  * STUCK_ARCHIVER: Indicates the primary database failed to archive due to disk being full.
    
  * DATAFILE_WRITE_ERRORS: Indicates failure to write dirty pages for the primary database.

  * DATAFILE_NO_SPACE: Indicates failure to create or write to a data file due to disk being full for the primary database.

  * SLICE_NO_SPACE: Indicates failure to create or write to slice files due to disk being full for the primary database.

The process for conditional failover is as follows:

1. Configure the conditions for automatic switching as needed.

  ```sql
  -- This is merely an example; please configure according to actual needs
  -- Set the primary database to close and trigger election upon redo write failure or dbwr writing dirty pages failure
  ALTER SYSTEM SET FAILOVER_HEALTH_CONDITION = 'INACCESSIBLE_LOGFILE, DATAFILE_WRITE_ERRORS' scope = both;

  -- Set the primary database to close and trigger election upon throwing database error 311
  ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311' scope = both;

  -- Set the primary database to close and trigger election upon throwing database error 311 and the corresponding system error code being 28
  ALTER SYSTEM SET FAILOVER_ERROR_CONDITION = '311-28' scope = both;
  ```
  
  >  **Note**: 
  >
  > Only when leader election is enabled will configuration related to conditional failover parameters take effect when the database is primary, and these settings will not automatically synchronize to standby databases.

2. Once the primary database encounters the exceptions or errors specified by the switching conditions during operation, it will attempt to synchronize the remaining redo logs to the standby database and then immediately shut down (terminate the YASDB process).

3. The standby database will trigger a heartbeat timeout and initiate an election to select a new primary database.

<span id="close_election" name="close_election" class="yaslink"></span>

## Disable Leader Election

The switch for leader election is controlled by the HA_ELECTION_ENABLED parameter. To disable leader election functionality, simply set this parameter to FALSE.

To avoid mistakenly triggering the leader election mechanism, follow the operation sequence of **standby first, primary later** when disabling leader election.

### Standalone Deployment High Availability Environment

In a Standalone Deployment high availability environment, the HA_ELECTION_ENABLED value must be modified sequentially on all standby databases and the primary database.

```sql
-- Disabling leader election must strictly adhere to the order of standby database first, then primary database
-- Operate on standby database 1, standby database 2, ... standby database n in succession
ALTER SYSTEM SET HA_ELECTION_ENABLED=FALSE;

-- Operate on the primary database
ALTER SYSTEM SET HA_ELECTION_ENABLED=FALSE;
```

### ISC Distributed High Availability Environment

In an ISC distributed high availability environment, you can directly specify the group or specific nodes to modify the HA_ELECTION_ENABLED value.

```sql
-- Specify DN group to modify HA_ELECTION_ENABLED value, effective for all DN groups
ALTER SYSTEM SET ha_election_enabled = FALSE SCOPE = BOTH TYPE = DN;

-- Specify MN group to modify HA_ELECTION_ENABLED value, effective for all MN groups
ALTER SYSTEM SET ha_election_enabled = FALSE SCOPE = BOTH TYPE = MN;
  
-- Specify a particular node to modify HA_ELECTION_ENABLED value, effective only for that node
ALTER SYSTEM SET ha_election_enabled = FALSE SCOPE = BOTH NODE = 3-1;
```
