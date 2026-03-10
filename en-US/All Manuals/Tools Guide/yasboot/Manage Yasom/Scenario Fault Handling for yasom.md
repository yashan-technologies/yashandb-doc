In actual usage, the yasom process may encounter abnormal scenarios due to reasons such as network or server issues. Please refer to the following solutions:

## Main yasom Process Failure

**Failure Phenomenon**

When executing the main yasom-specific functionality, an error "connection refused" occurs.

**Solution**

Option 1: On a server without yasom, recover a main yasom by executing `yasboot process yasom recover -c yashandb -l 192.168.1.3:1675 --role primary`.

Option 2: On a server that already has a backup yasom, promote the backup to primary by executing `yasboot process yasom recover -c yashandb --role primary`.

## Multiple Primary yasom Instances

**Failure Phenomenon**

Executing any operation results in the error "primary yasom is inconsistent".

**Possible Cause**

After an abnormal network communication, a new primary yasom was manually recovered. When the network is restored, the original primary yasom coexists with the new primary yasom, resulting in multiple primary instances.

**Problem Location**

```shell
$ yasboot process yasom status -c yashandb 
+-------------------------------------------------------------------------------------------------------------------+
| hostid   | pid   | ipaddr      | primary          | secondary | local_yasom_addr | role    | backup_num | max_seq |
+-------------------------------------------------------------------------------------------------------------------+
| host0001 | 16806 | 192.168.1.2 | 192.168.1.2:1675 | []        | 192.168.1.2:1675 | primary | 4          | 41      |
+----------+-------+-------------+------------------+-----------+------------------+---------+------------+---------+
| host0002 | 25832 | 192.168.1.3 | 192.168.1.3:1675 | []        | 192.168.1.3:1675 | primary | 5          | 43      |
+----------+-------+-------------+------------------+-----------+------------------+---------+------------+---------+
| host0003 | -     | 192.168.1.4 | 192.168.1.3:1675 | []        | -                | -       | 5          | 43      |
+----------+-------+-------------+------------------+-----------+------------------+---------+------------+---------+
```

If similar results are returned as above, it indicates that there are currently two primary yasom processes in the database cluster.

**Solution**

- Option 1: Automatic Recovery

    In an environment where [yasom arbitration](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby) is not enabled, you can enable [yasom auto-healing](Configuring Auto-Healing Function for yasom).

    After enabling the auto-healing functionality, when a multi-primary anomaly occurs, it will attempt to retain one primary yasom and automatically demote the remaining yasom instances. The demoted yasom will back up its metadata to $YASDB_HOME/om/{cluster name}/data/repair/.
    
    If the problem is not resolved, you may try manual recovery.

- Option 2: Manual Recovery

    Manually [clean](Managing Primary-Secondary yasom.html#clean)/[demote](Managing Primary-Secondary yasom.html#demote) the excess primary yasom processes.

## Primary/Standby Configuration Mismatch

**Failure Phenomenon**

Executing `yasboot process yasom status` shows inconsistent primary and secondary information between the servers.

```shell
$ yasboot process  yasom status -c yashandb
+------------------------------------------------------------------------------------------------------------------------------+
| hostid   | pid   | ipaddr      | primary          | secondary          | local_yasom_addr | role      | backup_num | max_seq |
+------------------------------------------------------------------------------------------------------------------------------+
| host0001 | 28550 | 192.168.1.2 | 192.168.1.3:1675 | [192.168.1.2:1675] | 192.168.1.2:1675 | secondary | 5          | 43      |
+----------+-------+-------------+------------------+--------------------+---------------------+-----------+------------+------+
| host0002 | 25832 | 192.168.1.3 | 192.168.1.3:1675 | [192.168.1.2:1675] | 192.168.1.3:1675 | primary   | 5          | 43      |
+----------+-------+-------------+------------------+--------------------+------------------+-----------+------------+---------+
| host0003 | -     | 192.168.1.4 | 192.168.1.3:1675 | []                 | -                | -         | 5          | 43      |
+----------+-------+--------------+-----------------+--------------------+------------------+-----------+------------+---------+
```

**Possible Cause**

During a recover or clean operation, if a yasagent crashes or times out, the new yasom information may not be synchronized to that server.

**Solution**

Log in as the installation user on any server and execute `yasboot process yasom sync` to synchronize the data.