## Scaling Abnormalities

If there are failures of existing nodes or other exceptions during scaling performed by *yasboot*, please refer to the following methods for corresponding countermeasures.

### Existing Node Failure During Scaling

- During the scaling of the DN group, if MN group nodes, CN nodes, or other DN groups not involved in scaling experience exceptions, the scaling task may be suspended. At this point, it is necessary to manually restore the normal operation of the relevant group before continuing with the scaling operations of the DN group.

- If the standby database of the DN group to be scaled down is abnormal and cannot be restored, directly scaling down may fail. You can delete the standby database through the node deletion functionality before scaling down.

### Handling Scaling Failures

If scaling fails, you need to execute the command `yasboot node/group remove --clean` to clean up the environment. It is not possible to perform start/stop/restart operations on the instance node that failed to scale using *yasboot*.

1. Log in to the current database cluster from the server where the *yasboot* tool is located with the installation user.

2. Execute the following commands to clean up the failed group, standby database, or nodes.
    
    ::: tabs
    == Clean up node
If the scale-out operation for [standby database](./单机主备扩缩容/备库扩缩容), [YAC instance](./共享集群扩缩容/集群实例扩缩容), [distributed cluster node](./分布式集群扩缩容/CN节点扩缩容) fails, execute the following command to clean up residual data caused by the failed scale-out.
```shell
$ yasboot node remove --clean --force -c yashandb
```
    == Clean up standby cluster
If the [standby cluster](./共享集群扩缩容/备集群扩缩容) scale-out operation fails, execute the following command to clean up residual data and processes caused by the failed scale-out.
```shell
yasboot group remove -c yashandb --group-ids 3 --purge -ce --clean
```
    == Clean up DN group
If the [distributed cluster node](./存算一体分布式集群扩缩容/DN组扩缩容) scale-out operation fails, execute the following command to clean up residual configurations and processes caused by the failed scale-out.
```shell
yasboot group remove -c yashandb --group-ids 3 --purge
```
    :::

3. If it is not possible to successfully clean up the corresponding standby database/node, you can try the following operations:

    1. ) Force stop the node that failed to scale:

        ```shell
        $ yasboot node stop -c yashandb -n {nodeid} --force
        ```
    
    2. ) Force delete the node that failed to scale:

        ```shell
        $ yasboot node remove -c yashandb -n {nodeid} --force
        ```

## Common Scaling Issues

When issues arise during scaling, the reasons can typically be identified by checking the GV$REPLICATION_EVENT/V$REPLICATION_EVENT, GV$DIAG_INCIDENT/V$DIAG_INCIDENT views or the log files.

### Primary/Standby Connection Anomalies

- If error code 113 appears: Firewall issues; a whitelist needs to be configured on the server of the primary standby database (cluster), or the firewall should be disabled.

- If error code 111 appears: The peer database is not started, or the IP port configuration in the parameters is incorrect.

### Build or Restore Failures

- If it is a standalone primary/standby deployment, check the local_fs directory first; the content of the local_fs directory must be deleted before building, otherwise the build will fail.

- File creation failed, and the path does not exist, which may be due to the standby database/backup cluster's folder not being fully created; manual creation is required.

- File creation failed, and the file already exists; this may be because previous files have not been cleaned up properly. All ctrl, data, redo, and archive files should be cleaned before the build. It may also be that both primary and standby are deployed on the same server, but the conversion path for the standby database/backup cluster has not been configured, leading to overlapping file paths. The correct conversion path should be set.

- Error received: timeout; this could be due to low network bandwidth, or the primary database/main cluster is set to maximize protection mode, but the standby database/backup cluster has not been completely deployed, causing primary database/main cluster transactions to be stuck. It is required to change to maximize availability mode, and once deployment is completed, it can be reverted.

### Standby Database/Backup Cluster NEED REPAIR

- Primary/standby logs do not match

    In maximize availability or maximize performance mode, some logs may not have been synchronized to the standby database before the original primary database went down. After a failover to the standby database and dropping the original primary database, there will be discrepancies between the logs of the original primary database and the new primary database. Users need to decide whether to roll back the old primary database logs to eliminate the log discrepancy (once rolled back, recovery of those logs will not be possible), allowing the standby database to properly synchronize the logs of the new primary database. Users can quickly fix this using the SQL statement ALTER SYSTEM IGNORE STANDBY MISMATCHED REDO.

- Archive log files missing

    - The primary database's archiving cleanup parameters are configured incorrectly, leading to cleaning of primary database archive files.

    - Archive files of the primary database are accidentally lost or deleted.

- File content is corrupted and cannot sync

    Online redo files or archive log files of the standby database have bad blocks, preventing the standby database from synchronizing properly.

- Primary/standby data inconsistencies, brain split

    Some logs may not have been synchronized to the standby database before the original primary database went down. After a failover to the standby database and dropping the original primary database, there will be discrepancies between the logs of the original primary database and the new primary database, and if the divergent logs have been committed and cannot be rolled back, this will result in a brain split between primary and standby, causing data inconsistency.

- Primary/standby metadata inconsistency

    - The binary versions of primary/standby do not match; the version needs to be changed.

    - The primary/standby database_id is different, which is usually because the primary database/main cluster has been rebuilt while the standby database/backup cluster has not, leading to database mismatch.

- SLICE files missing

    Incorrect parameter configuration of the Primary database leads to cleaning of the Primary database slice files. In this case, the standby database needs to be rebuilt.

- Database failure

    The standby database experiences a FATAL error, preventing it from properly synchronizing with the primary database, marking the standby database as needing repair and informing the primary database. Common causes include insufficient resources, file operation failures, etc.; users can examine the log files to determine the exact FATAL error. This situation requires DBA intervention to repair the failure and restart the database.

> **Note**: 
>
> The first four scenarios of needing repair for standby databases can be attempted to be fixed using the BUILD DATABASE REPAIR STANDBY command. Please refer to [Repairing Abnormal Standby Database](../../High Availability/Standby Repair) for specific operations.
>
> The primary/standby logs not matching is also a scenario that needs repair, but the status of the standby database will show REDO MISMATCH. For details, please refer to [Manual Switching of Primary/Standby](../../High Availability/Primary-Standby Manual Switching).

### Cascade Backup Cannot Connect to Upper-Level Standby Database

This may be due to incorrect link parameter settings, or the parameters need to be applied after restarting the instance.