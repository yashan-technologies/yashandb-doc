This example demonstrates the backup and recovery integration via the XBSA API. The users, passwords, IPs, ports, paths, etc., listed in the text need to be replaced with actual values.

>**Note**:
>
> In the ISC Distributed Cluster Deployment, modifying the node's IP is supported. This allows the use of a backup set from Cluster A to restore Cluster B when the deployment shapes of the two distributed clusters are completely consistent. To use cross-distributed cluster recovery, the following conditions must be met:
>
>- The IPs in the DIN_ADDR and REPLICATION_ADDR of the B cluster's configuration file must be consistent with LISTEN_ADDR, and the port numbers of DIN_ADDR and REPLICATION_ADDR must be completely consistent with those of Cluster A; otherwise, the cluster cannot be restored properly.
>
>- Before executing the recovery operation, all backup sets from the distributed cluster A must be copied to the corresponding node backup directory of Cluster B.

1. Refer to [YashanDB Server Installation (Command Line)](../../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/00YashanDB Installation via CLI) document to complete the database installation and deployment.

2. Enter the installation directory and check all nodes of the database to determine if all nodes have archiving mode (ARCHIVELOG) enabled. If it is not enabled, please refer to [Archive Management](../../Database Administration/Basic Database Management/Archive Management) to enable archiving.

    ```shell
    $ yasboot sql -d sys/password@192.168.1.2:1688

    SQL> SELECT database_name,log_mode,open_mode FROM V$DATABASE;
    
    DATABASE_NAME      LOG_MODE          OPEN_MODE  
    ------------------ ----------------- ------------
    yasdb              ARCHIVELOG        READ_WRITE 
    ```

3. Synchronize and unpack the YashanDB installation package on the backup server, which needs to have the XBSA dynamic library.

4. Obtain the connection token for *yasom* which configured as `rpc_secret_key` in the file `${YASDB_HOME}/om/<Cluster_name>/conf/tls.toml`, and then initialize *yasbak*.

    ```shell
    $ yasbak deploy -c yashandb -a 192.168.1.3:1675 -k 96ed7a2c90e81a9e -D ./catalog -u sys -p password
    +-------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path            |
    +-------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | db        | 1-1:1  | 26097 | open            | normal          | primary       | 192.168.1.3:1688 | /data/yashan/db-1-1  |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    |          | db        | 1-2:2  | 26220 | open            | normal          | standby       | 192.168.1.3:1690 | /data/yashan/db-1-2  |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    |          | db        | 1-3:3  | 26215 | open            | normal          | standby       | 192.168.1.3:1692 | /data/yashan/db-1-3  |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    cluster:yashandb deploy yasbak succeed
    ```

5. Perform the backup operation.

    yasrman provides an interface for integrating with third-party XBSA APIs. The PARAMS parameter needs to be added to the SQL statement; for details, please refer to [yasrman params introduction](../yasrman/User Guide for yasrman/00User Guide for yasrman).

    ```shell
    YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"

    # Perform full backup
    $ yasbak run -c yashandb -s "backup database tag 'full_01' parallelism 4 dest client params '${YASRMAN_PARAM}'"
    
    # Perform incremental backup
    $ yasbak run -c yashandb -s "backup database incremental level 0 tag 'incr_0'  dest client params '${YASRMAN_PARAM}'"
    $ yasbak run -c yashandb -s "backup database incremental level 1 tag 'incr_1'  dest client params '${YASRMAN_PARAM}'"
    
    # Clean backup
    $ yasbak run -c yashandb -s "delete backupset tag 'full_01'"
    ```
    Track the run logs in `$YASDB_DATA/log/run/run.log` for detailed logs of the backup and recovery process for each node.

6. Reset the database to nomount.

    Before recovery, the database needs to be reset. Database reset is a high-risk operation that requires the sys user password. Use the yasbak reset command to reset the database.

    ```shell
    # Restore the database to nomount state
    ./yasbak reset -c yashandb -p password
    ```

7. Perform the recovery operation.

    ```shell
    YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"
    # Restore
    $ yasbak run -c yashandb -s "restore database from tag 'full_01' dest client params '${YASRMAN_PARAM}'" --build-all
    ```

8. (Optional) For environments where *yasbak* operations are not required, *yasbak* can be uninstalled.

    ```shell
    # Specifying --purge will also delete the catalog directory
    $ yasbak clean -c yashandb --purge

    remove yasbak.env succeed.
    remove yasrman catalog succeed.
    ```
