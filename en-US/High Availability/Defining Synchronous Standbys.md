In the maximize availability or maximize protection mode, the primary database transaction submission must wait for the redo synchronization from the standby database, which is referred to as synchronous standby. By default, the synchronous standby is configured to require a majority of the standby databases to receive the redo before the primary database transaction can be submitted successfully.

>**Note**:
>
> In Standalone Deployment, it should be referred to as synchronous standby database; in YAC Deployment, it should be referred to as synchronous standby cluster; in ISC Distributed Cluster Deployment, it should be referred to as synchronous standby nodes for each group.
>
> The subsequent text will uniformly adopt the terms "primary database" and "standby database."

YashanDB supports users to customize the synchronous standby through the parameters QUORUM_SYNC_STANDBYS and REQUIRED_SYNC_STANDBYS, allowing flexible adjustment of the synchronization mechanism for successful primary database transactions. When customizing configurations, the following rules must be followed:

- Different deployment forms behave differently:

    - In Standalone Deployment, both parameters support online setting and take effect immediately.

    - In YAC Deployment, the REQUIRED_SYNC_STANDBYS parameter configuration must be consistent across all instances; the QUORUM_SYNC_STANDBYS parameter does not take effect.

    - In ISC Distributed Cluster Deployment, the nodes within the same group should have consistent configurations; otherwise, exceptions may occur. Both parameters should be configured during installation, and **it is not recommended** to modify them afterwards.

- Custom synchronous standby is effective only in maximize protection mode, and both parameters must be satisfied for the primary database transaction to be submitted successfully. For example, setting QUORUM_SYNC_STANDBYS='ANY 2 (*)' and REQUIRED_SYNC_STANDBYS='standby2' means that after redo is synchronized to standby database 2 and one other standby database, the primary database transaction can be submitted successfully.
    
- The settings of the primary database will not be synchronized to the standby database, meaning each node can independently set the above parameters, but it is recommended that the corresponding parameter values be consistent across all nodes in the same environment. When any of the above two parameters are configured inconsistently across the nodes, the entire database environment will always follow the configuration of the primary database (the primary/standby role switch may cause configuration changes).

- If leader election is enabled, the aforementioned parameters must be consistent across all nodes in the same environment (otherwise database inconsistency issues will arise), and specific standby database names may not be included in the values of both parameters.

- Only the REQUIRED_SYNC_STANDBYS parameter can be specified as a standby database with ARCHIVE_DEST_x link configuration item DISABLE_ELECTION = TRUE.

<span id="REQUIRED_SYNC_STANDBYS" name="REQUIRED_SYNC_STANDBYS"></span>

## REQUIRED_SYNC_STANDBYS

The REQUIRED_SYNC_STANDBYS parameter indicates that the primary database transaction can only be submitted successfully after all specified standby databases have received the redo logs. The parameter value format is `'*'` or `'standby_name1, standby_name2, …'`.

- `*` signifies all current standby databases.

- `standby_name1, standby_name2, …` signifies a specific list of standby databases, which must use the real names and cannot be repeated. The names of standby databases can be retrieved from the DB_UNIQUE_NAME field of the [GV$ARCHIVE_DEST](../Reference Manual/System Views/Dynamic Performance Views/GV$ARCHIVE_DEST) view.

***Example***

```sql
-- Query standby database information
SELECT dest_name,service,db_unique_name,node_id FROM GV$ARCHIVE_DEST;
DEST_NAME         SERVICE                                                          DB_UNIQUE_NAME                    NODE_ID
----------------- ---------------------------------------------------------------- --------------------------------- ----------------------------------------------------------------
ARCHIVE_DEST_1    192.168.1.3:1689                                                standby1                          1-2
ARCHIVE_DEST_2    192.168.1.4:1689                                                standby2                          1-3

-- Ensure that the transaction of the primary database is only submitted after standby2 receives the redo
ALTER SYSTEM SET REQUIRED_SYNC_STANDBYS ='standby2' SCOPE=BOTH;

-- Ensure that the transaction of the primary database is only submitted after all standby databases receive the redo
ALTER SYSTEM SET REQUIRED_SYNC_STANDBYS ='*' SCOPE=BOTH;
```

## QUORUM_SYNC_STANDBYS

The QUORUM_SYNC_STANDBYS parameter indicates that the primary database transaction can only be successfully submitted when the specified condition of standby databases receiving redo logs is met, and may not be specified as a standby database with ARCHIVE_DEST_x link configuration item DISABLE_ELECTION = TRUE. The parameter value format is `MAJORITY` or `ANY syncNum (standby database list)`.

- `MAJORITY`: This is the default value, indicating that the primary database transaction can only be submitted if at least (N-d)/2 standby databases have received redo logs. If the result is a decimal, it is rounded down.

    - N: The total number of nodes (including the primary database).

    - d: The number of standby databases that do not participate in the majority voting and are not counted as receiving the redo logs, where the ARCHIVE_DEST_x link parameter DISABLE_ELECTION = TRUE.

- `ANY syncNum (standby database list)`: This indicates that the primary database transaction can be submitted if at least syncNum standby databases in the specified list have synchronized the redo logs. The configuration requirements are as follows:

    - ANY: Must be in uppercase and cannot be omitted.

    - syncNum's value: Cannot be zero and cannot exceed the number of standby databases in the `()` list. This indicates that at least syncNum standby databases in this group must receive the redo logs for the primary database transaction to be submitted.

    - Standby database list: Must not be empty, with a maximum length of 32 characters. Configuring as `*` indicates all standby databases. If you directly list standby databases, the format should be `standby_name1, standby_name2, …`, using real standby database names that cannot be repeated. The names of standby databases can be retrieved from the DB_UNIQUE_NAME field of the [GV$ARCHIVE_DEST](../Reference Manual/System Views/Dynamic Performance Views/GV$ARCHIVE_DEST) view.

    > **Caution**: 
    >
    > The QUORUM_SYNC_STANDBYS parameter cannot be set to standby databases with ARCHIVE_DEST_x link configuration item DISABLE_ELECTION = TRUE. If all standby databases in the list are DISABLE_ELECTION = TRUE, an error will be reported.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
-- The default value of QUORUM_SYNC_STANDBYS is MAJORITY
SHOW PARAMETER QUORUM_SYNC_STANDBYS;
NAME                            VALUE                
------------------------------- -------------------- 
QUORUM_SYNC_STANDBYS            MAJORITY     

-- The primary database transaction submission requires that at least one of standby database 2 or standby database 3 receives the redo
ALTER SYSTEM SET QUORUM_SYNC_STANDBYS='ANY 1 (standby2, standby3)' SCOPE=BOTH;

-- The primary database transaction submission requires that at least one of all standby databases receives the redo
ALTER SYSTEM SET QUORUM_SYNC_STANDBYS='ANY 1 (*)' SCOPE=BOTH;
```
