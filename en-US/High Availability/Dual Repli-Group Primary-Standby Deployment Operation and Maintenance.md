The deployment of the dual-replication group primary/standby configuration is achieved by implementing two sets of standalone replication groups, enabling data replication from one replication group (the primary group) to another (the standby group). This deployment scheme is recommended for remote disaster recovery scenarios. For installation guidelines, please refer to [Dual Replication Group Primary/Standby Deployment](../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/Standalone (Primary-Standby) Deployment.md#Manual).

## Usage Rules

When adopting the dual-replication group primary/standby deployment, the following usage rules must be adhered to:

- The one-primary/one-standby Yasom election is not suitable for the dual-replication group primary/standby deployment.
- When using a one-primary/multi-standby configuration within the primary replication group, [leader election](Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby) can be enabled, and nodes in the standby replication group do not participate in leader election. After the installation of the dual-replication group primary/standby deployment, if the number of primary standby databases in the primary replication group is greater than or equal to 3, leader election will be configured to enable.
- The [inter-group failover](#failovergroups) to promote the standby group to primary replication group can only be executed when all nodes in the primary replication group are unavailable; otherwise, a dual-primary situation will occur.
- Before executing the [inter-group switchover](#swgroups), leader election for the primary replication group must be disabled to prevent automatic promotion after the primary is demoted.
- If the primary node of the standby group fails and cannot accept redo logs from the primary replication group, the standby group becomes unavailable, and the issue can only be resolved by manually switching the redo receiving node in the standby group. For specific operations, please refer to [Role Switch in Standby Group](#failoveringroup).

## ARCHIVE_DEST_* Parameter Configuration

After the YashanDB installation for the dual replication group disaster recovery deployment, the following command can be executed to check the database status.

```shell
$ yasboot cluster status -c yashandb -d

+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid  | node_type | nodeid | pid  | instance_status | database_status | database_role | source_node | listen_address  | data_path             |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db     | 1-1:1  | 73132 | open       | normal      | primary    | -      | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0002 | db     | 1-2:2  | 73199 | open       | normal      | standby    | 1-1:1    | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0003 | db     | 1-3:3  | 73201 | open       | normal      | standby    | 1-1:1    | 192.168.1.4:1688  | /data/yashan/yasdb_data/db-1-3   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0004 | db     | 2-1:4  | 73362 | open       | normal      | standby    | 1-1:1    | 192.168.10.5:1688 | /data/yashan/yasdb_data/db-2-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0005 | db     | 2-2:5  | 73435 | open       | normal      | standby    | 2-1:4    | 192.168.10.6:1688 | /data/yashan/yasdb_data/db-2-2   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0006 | db     | 2-3:6  | 73499 | open       | normal      | standby    | 2-1:4    | 192.168.10.7:1688 | /data/yashan/yasdb_data/db-2-3   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
```

Based on the information displayed, there are two groups: Group 1 (primary replication group) and Group 2 (standby replication group), each containing three nodes. Assuming NODE1-1 is the primary database and NODE2-1 is the primary node of the standby group, then NODE1-2, NODE1-3, and NODE2-1 are standby databases, while NODE2-2 and NODE2-3 are cascade standbys.

### Primary Replication Group

As the primary replication group, the configuration requirements for nodes within Group 1 are as follows:

- The primary replication group supports enabling leader election functionality. All nodes should have HA_ELECTION_ENABLED=TRUE, and all three nodes NODE1-1, NODE1-2, and NODE1-3 must participate in leader election.
- The primary database in the primary replication group (which could be NODE1-1, NODE1-2, or NODE1-3) must send redo logs to the primary node NODE2-1 of the standby group. Therefore, all nodes in Group 1 must configure ARCHIVE_DEST_4=NODE2-1. Since the node NODE2-1 in the standby group does not participate in leader election, DISABLE_ELECTION=TRUE should also be configured in the ARCHIVE_DEST_4 link.

```sql
-- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
-- NODE1-1:
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=NODE2-1 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE

-- NODE1-2:
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_1=NODE1-1 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=NODE2-1 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE

-- NODE1-3:
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=NODE2-1 DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE
```

### Standby Replication Group

As the standby replication group, the configuration requirements for nodes within Group 2 are as follows:

- The standby replication group does not participate in leader election. All nodes must have HA_ELECTION_ENABLED=FALSE.
- NODE2-1, as the connecting node of the primary replication group, acts both as the standby database for the primary database and as the upper-level standby database for cascade standbys. Therefore, its configuration differs from that of other nodes within the group. Regardless of NODE2-1's role, it must send logs to NODE2-2 and NODE2-3, so the effective roles for ARCHIVE_DEST_* parameters pointing to NODE2-2 and NODE2-3 should be ALL_ROLES.
- In the event of a primary/standby switch between groups, after Group 2 becomes the new primary replication group, it must send logs to the new standby node NODE1-1. Thus, the effective role of the ARCHIVE_DEST_1 parameter pointing to NODE1-1 for all nodes within Group 2 should be set to PRIMARY_ROLE (which is effective when the current node is a primary database).

```sql
-- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
-- NODE2-1: 
	HA_ELECTION_ENABLED=FALSE
    ARCHIVE_DEST_5=NODE2-2  VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE  

-- NODE2-2: 
    HA_ELECTION_ENABLED=FALSE
    ARCHIVE_DEST_4=NODE2-1  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE         

-- NODE2-3: 
    HA_ELECTION_ENABLED=FALSE
    ARCHIVE_DEST_4=NODE2-1  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_5=NODE2-2  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE
```

## Database Creation Instructions

The essence of the dual-replication group primary/standby deployment is still the one-primary/multi-standby deployment model with multiple cascade standbys. To recreate the database, the primary database must be created first, followed by executing the BUILD operation for its standby database and cascade standby database. When initializing multiple standby databases, the build operation can be initiated in parallel by the primary database.

1. Execute the following commands on the primary database (NODE1-1) in the primary replication group to check the status of the standby databases (NODE1-2, NODE1-3, NODE2-1) and initialize them.

    ```sql
    -- Execute on NODE1-1
    -- The v$archive_dest_status view displays connectivity information between the primary database and all standby databases. When the CONNECTION field is CONNECTED, it indicates that the primary/standby network is connected and standby database initialization can proceed.
    SELECT CONNECTION,STATUS,DATABASE_MODE FROM v$ARCHIVE_DEST_STATUS;
    
    -- Initiate parallel BUILD
    BUILD DATABASE TO STANDBY (*);    
    ```

2. Execute the following commands on the primary node of the standby group (NODE2-1) to check the status of the cascade standby databases (NODE2-2, NODE2-3) and initialize them.

    ```sql
    -- Execute on NODE2-1
    SELECT CONNECTION,STATUS,DATABASE_MODE FROM v$ARCHIVE_DEST_STATUS;
    
    -- Initiate parallel BUILD
    BUILD DATABASE TO STANDBY (*);    
    ```

3. After all standby databases have completed initialization and synchronization, verify the database roles and synchronization status of the standby databases.

    1. On the primary database (NODE1-1) of the primary replication group, execute the following commands to check the operational status of the standby databases (NODE1-2, NODE1-3, NODE2-1).

        ```sql
        -- Execute on NODE1-1
        SELECT CONNECTION,STATUS,DATABASE_MODE FROM v$ARCHIVE_DEST_STATUS;
        ```

    2. On the primary node of the standby group (NODE2-1), execute the following commands to check the operational status of the cascade standbys (NODE2-2, NODE2-3).

        ```sql
        -- Execute on NODE2-1
        SELECT CONNECTION,STATUS,DATABASE_MODE FROM v$ARCHIVE_DEST_STATUS;
        ```

## Manual Switching Between Primary/Standby

Manual switching between primary and standby can be categorized into:

- Inter-group primary/standby switching: [Inter-group Switchover](#swgroups), [Inter-group Failover](#failovergroups)
- Intra-group node primary/standby switching: manual switchover and failover within the primary replication group (same as [Primary-Standby Manual Switching](Primary-Standby Manual Switching)), and [Role Switch within Standby Group (Failover)](#failoveringroup)

<span id="swgroups" name="swgroups"></span>

### Inter-group Switchover

Executing a switchover on the primary node of the standby group is equivalent to a group-level primary/standby role switch, requiring manual modification of certain configuration parameters.

The configuration example in this article: Group 1 is the primary replication group, Group 2 is the standby replication group, NODE1-1 is the primary database, NODE2-1 is the primary node of the standby group, NODE1-2, NODE1-3, NODE2-1 are standby databases, NODE2-2, NODE2-3 are cascade standbys.

1. Sequentially disable the leader election functionality for all nodes in the primary replication group Group 1, following the order of first the standby databases and then the primary database, i.e., first disable the corresponding configuration for NODE1-3, NODE1-2, and finally NODE1-1.


    ::: tabs
    == Operate using yasboot

```shell
# Log in to the database installation server using the installation user

# Check the parameter value of HA_ELECTION_ENABLED; if it is TRUE, it means it is enabled.
$ yasboot cluster config show -c yashandb -q HA_ELECTION_ENABLED
+------------------------------+
| node   | HA_ELECTION_ENABLED |
+------------------------------+
| db-2-2 | FALSE               |
+--------+---------------------+
| db-2-1 | FALSE               |
+--------+---------------------+
| db-2-3 | FALSE               |
+--------+---------------------+
| db-1-3 | TRUE                |
+--------+---------------------+
| db-1-2 | TRUE                |
+--------+---------------------+
| db-1-1 | TRUE                |
+--------+---------------------+

# Disable leader election for Group 1 before proceeding with the next operations
$ yasboot group config set -c yashandb -g 1 -k HA_ELECTION_ENABLED -v FALSE
```
    
    == Operate using SQL statements

```sql
-- Connect and log in to the target database node as a DBA user

-- Check the parameter value of HA_ELECTION_ENABLED; if it is TRUE, it means it is enabled.
SHOW PARAMETER HA_ELECTION_ENABLED

-- Disable leader election before proceeding with the next operations (Closing order: first standby databases then primary databases)
-- Execute on NODE1-3
ALTER SYSTEM SET HA_ELECTION_ENABLED=FALSE;

-- Execute on NODE1-2
ALTER SYSTEM SET HA_ELECTION_ENABLED=FALSE;

-- Execute on NODE1-1
ALTER SYSTEM SET HA_ELECTION_ENABLED=FALSE;
```
    :::

2. Modify the ARCHIVE_DEST_* configuration parameters for the primary node NODE2-1 in the standby group, changing the effective roles of ARCHIVE_DEST_* pointing to other nodes in the same group to PRIMARY_ROLE.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE2-1: 
    ARCHIVE_DEST_5=NODE2-2  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE  
    ```   

3. Execute the switchover operation to promote NODE2-1 as the new primary.

    ::: tabs
    == Operate using yasboot

```shell
$ yasboot node switchover -c yashandb -n 2-1
```

    == Operate using SQL statement

```sql
-- Execute on NODE2-1
ALTER DATABASE SWITCHOVER;
```
   :::

4. After the promotion is completed, modify the ARCHIVE_DEST_* configuration parameters for NODE1-1, changing the effective range of ARCHIVE_DEST_* pointing to other nodes in the same group to ALL_ROLES.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE1-1:
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_4=NODE2-1 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE
    ```

5. Sequentially enable the leader election functionality for all nodes in the new primary replication group Group 2, following the order of first the primary database and then the standby databases, i.e., first enable the corresponding configuration for NODE2-1, then for NODE2-2, and NODE2-3.
    
    ::: tabs
    == Operate using yasboot

```shell
$ yasboot group config set -c yashandb -g 2 -k HA_ELECTION_ENABLED -v TRUE
```
    == Operate using SQL statements

```sql
-- Enabling order: first primary database then standby databases 
-- Execute on NODE2-1
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
   
-- Execute on NODE2-2
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
   
-- Execute on NODE2-3
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
```
    :::

At this point, the inter-group switching between primary/standby is complete. Group 1 becomes the standby replication group, with NODE1-1 serving as the standby group's primary node and NODE1-2, NODE1-3 becoming cascade standbys. Group 2 becomes the primary replication group, with NODE2-1 as the primary database and NODE2-2, NODE2-3 as standby databases.

<span id="failovergroups" name="failovergroups"></span>

### Inter-group Failover

Inter-group failover can only be executed to promote the standby group to the primary replication group when all nodes in the primary replication group are unavailable; otherwise, a dual-primary situation will occur. After completing the failover, all nodes in the old primary replication group must be manually restarted to ensure normal operation under the dual-replication group mode.

The configuration example in this article: Group 1 is the primary replication group, Group 2 is the standby replication group, NODE1-1 is the primary database, NODE2-1 is the primary node of the standby group, NODE1-2, NODE1-3, NODE2-1 are standby databases, NODE2-2, NODE2-3 are cascade standbys.

1. Execute failover on the primary node NODE2-1 of the standby group.

    ::: tabs
    == Operate using yasboot

```shell
$ yasboot node failover -c yashandb -n 2-1
```

    == Operate using SQL statement

```sql
-- Execute on NODE2-1
ALTER DATABASE FAILOVER;
```
   :::

2. After the node is promoted, the ARCHIVE_DEST_* configuration parameters for the node must be modified, changing the effective roles of ARCHIVE_DEST_* pointing to other nodes in the same group to PRIMARY_ROLE.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE2-1: 
    ARCHIVE_DEST_5=NODE2-2  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE  
    ```

3. Sequentially enable the leader election functionality for all nodes in the new primary replication group Group 2, following the order of first the primary database and then the standby databases, i.e., first enable the corresponding configuration for NODE2-1, then for NODE2-2, and NODE2-3.

    ::: tabs
    == Operate using yasboot

```shell
$ yasboot group config set -c yashandb -g 2 -k HA_ELECTION_ENABLED -v TRUE
```
    == Operate using SQL statements

```sql
-- Enabling order: first primary database then standby databases 
-- Execute on NODE2-1
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
   
-- Execute on NODE2-2
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
   
-- Execute on NODE2-3
ALTER SYSTEM SET HA_ELECTION_ENABLED=TRUE;
```
    :::

4. Start all nodes in the old primary replication group Group 1 to the NOMOUNT stage and disable their leader election.

   ```shell
   $ yasboot group start -c yashandb -g 1 -m nomount
   $ yasboot group config set -c yashandb -g 1 -k HA_ELECTION_ENABLED -v FALSE
   ```

5. Start the old primary NODE1-1 to the MOUNT stage and perform the demotion operation.

    ```shell
    $ yasql sys/********@192.168.1.2:1688

    -- Start the old primary database (e.g., 1-1) to the MOUNT stage
    SQL> ALTER DATABASE MOUNT;
    
    -- Execute the demotion operation on NODE1-1
    SQL> ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
    ```


6. Modify the ARCHIVE_DEST_* configuration parameters for node NODE1-1, changing the effective roles of ARCHIVE_DEST_* pointing to other nodes in the same group to ALL_ROLES.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE1-1:
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_4=NODE2-1 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE
    ```

7. Sequentially start NODE1-1, NODE1-2, and NODE1-3 to the OPEN stage.

8. If the status of the new standby group (Group 1) is "need repair" upon starting, the standby database must be repaired or rebuilt as per the error messages.

<span id="failoveringroup" name="failoveringroup"></span>

### Role Switching within Standby Group

If the primary node of the standby group becomes unavailable due to failure or other reasons, another node must be switched to become the new primary node, accepting redo from the primary replication group.

The configuration example in this article: Group 1 is the primary replication group, Group 2 is the standby replication group, NODE1-1 is the primary database, NODE2-1 is the primary node of the standby group, NODE1-2, NODE1-3, NODE2-1 are standby databases, NODE2-2, NODE2-3 are cascade standbys. Assuming that the redo receiving instance in Group 2 needs to switch from NODE2-1 to NODE2-2.

1. Modify the ARCHIVE_DEST_* configuration parameters for NODE2-1, changing the effective roles of ARCHIVE_DEST_* pointing to other nodes in the same group to PRIMARY_ROLE.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE2-1: 
    ARCHIVE_DEST_5=NODE2-2  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE  
    ```

2. Modify the ARCHIVE_DEST_* configuration parameters for all nodes in Group 1 (the primary replication group), clearing the parameters pointing to NODE2-1 and adding new parameters pointing to NODE2-2.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE1-1:
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=''
    ARCHIVE_DEST_5=NODE2-2 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE
   
    -- NODE1-2: 
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_1=NODE1-1 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_3=NODE1-3 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=''
    ARCHIVE_DEST_5=NODE2-2 DISABLE_ELECTION=TRUE VALID_FOR=PRIMARY_ROLE
   
    -- NODE1-3: 
    HA_ELECTION_ENABLED=TRUE
    ARCHIVE_DEST_2=NODE1-2 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_1=NODE1-1 VALID_FOR=PRIMARY_ROLE
    ARCHIVE_DEST_4=''
    ARCHIVE_DEST_5=NODE2-2 DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE
    ```

3. Modify the ARCHIVE_DEST_* configuration parameters for NODE2-2, changing the effective roles of ARCHIVE_DEST_* pointing to other nodes in the same group to ALL_ROLES.

    ```sql
    -- The ARCHIVE_DEST_*=node* notation is a placeholder and should be replaced by the actual REPLICATION_ADDR of the target node
    -- NODE2-2: 
    ARCHIVE_DEST_4=NODE2-1  VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_6=NODE2-3  VALID_FOR=ALL_ROLES
    ARCHIVE_DEST_1=NODE1-1  DISABLE_ELECTION=TRUE  VALID_FOR=PRIMARY_ROLE   
    ```
