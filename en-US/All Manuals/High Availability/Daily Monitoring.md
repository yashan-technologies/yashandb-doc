Database administrators can check the running status and synchronization of the primary standby database through querying specific [system views](../Reference Manual/System Views/00System Views) to confirm key information in high availability environments, such as primary/standby roles, protection modes, synchronization performance, etc.

> **Note**: 
> 
> The views listed in this article exist in both primary and standby databases, while unique views indicate that the view only has records on specific nodes.

## Monitoring Through Views

### General Views for Primary Standby Database

#### GV$DATABASE/V$DATABASE

Database status view that records the DATABASE_ROLE of primary/standby roles, LOG_MODE for archiving mode, OPEN_MODE for read/write mode, PROTECTION_MODE for protection mode, and other information. For detailed field descriptions, please refer to [GV$DATABASE](../Reference Manual/System Views/Dynamic Performance Views/GV$DATABASE)/[V$DATABASE](../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE).

***Example*** for Standalone Deployment

```sql
SELECT database_name,log_mode,open_mode,protection_mode,database_role,block_size,status FROM  V$DATABASE; 
DATABASE_NAME  LOG_MODE    OPEN_MODE  PROTECTION_MODE      DATABASE_ROLE   BLOCK_SIZE STATUS
-------------- ----------- ---------- -------------------- ------------- ------------ -------
yashandb          ARCHIVELOG  READ_ONLY  MAXIMUM PERFORMANCE  STANDBY               8192 NORMAL
```

***Example*** for YAC Deployment

```sql
SELECT database_name,log_mode,open_mode,protection_mode,database_role,block_size,status FROM  V$DATABASE; 
DATABASE_NAME  LOG_MODE    OPEN_MODE  PROTECTION_MODE      DATABASE_ROLE   BLOCK_SIZE STATUS
-------------- ----------- ---------- -------------------- ------------- ------------ -------
yashandb          ARCHIVELOG  READ_WRITE MAXIMUM PERFORMANCE  PRIMARY               8192 NORMAL
```

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT group_id, group_node_id, database_name,log_mode,open_mode,protection_mode,database_role,block_size,status FROM GV$DATABASE;
  GROUP_ID  GROUP_NODE_ID DATABASE_NAME   LOG_MODE          OPEN_MODE         PROTECTION_MODE       DATABASE_ROLE       BLOCK_SIZE STATUS 
---------- -------------- --------------- ----------------- ----------------- --------------------- ----------------- ------------ -------
         1              1 yashandb        NOARCHIVELOG      READ_WRITE        MAXIMUM PERFORMANCE   PRIMARY                   8192 NORMAL 
         2              1 yashandb        NOARCHIVELOG      READ_WRITE        MAXIMUM PERFORMANCE   PRIMARY                   8192 NORMAL 
         3              1 yashandb        ARCHIVELOG        READ_WRITE        MAXIMUM PROTECTION    PRIMARY                   8192 NORMAL 
         3              2 yashandb        ARCHIVELOG        READ_ONLY         MAXIMUM PROTECTION    STANDBY                   8192 NORMAL 
         3              3 yashandb        ARCHIVELOG        READ_ONLY         MAXIMUM PROTECTION    STANDBY                   8192 NORMAL 
```

#### GV$ARCHIVED\_LOG/V$ARCHIVED\_LOG

Archive log files statistical view that contains information such as log members, log size, log number, and completion time. For detailed field descriptions, please refer to [GV$ARCHIVED_LOG](../Reference Manual/System Views/Dynamic Performance Views/GV$ARCHIVED_LOG)/[V$ARCHIVED_LOG](../Reference Manual/System Views/Dynamic Performance Views/V$ARCHIVED_LOG).

***Example*** for Standalone Deployment

```sql
SELECT name, SEQUENCE#, blocks, block_size, completion_time FROM V$ARCHIVED_LOG;
NAME                                                     SEQUENCE#       BLOCKS BLOCK_SIZE COMPLETION_TIME                  
----------------------------------------------------- ------------ ------------ ---------- ------------------
/data/yashan/yasdb_data/db-1-1/archive/arch_0_1.ARC              1         6440       4096 2025-06-20 05:20:48
/data/yashan/yasdb_data/db-1-1/archive/arch_0_2.ARC              2          335       4096 2025-06-20 05:20:58
```

***Example*** for YAC Deployment

```sql
SELECT name, inst_id, SEQUENCE#, blocks, block_size, completion_time FROM GV$ARCHIVED_LOG;

NAME                                                                 INST_ID    SEQUENCE#                BLOCKS   BLOCK_SIZE COMPLETION_TIME
---------------------------------------------------------------- ----------- ------------ --------------------- ------------ ------------------------------------------------
+DG0/arch_files/arch_0_1.ARC                                               1            1                 43096          512 2025-06-20 05:47:15
+DG0/arch_files/arch_0_2.ARC                                               1            2                 35773          512 2025-06-20 05:48:05
```

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT group_id, group_node_id, name, SEQUENCE#, blocks, block_size, completion_time FROM GV$ARCHIVED_LOG;

   GROUP_ID GROUP_NODE_ID NAME                                                                SEQUENCE#                BLOCKS   BLOCK_SIZE COMPLETION_TIME
----------- ------------- ---------------------------------------------------------------- ------------ --------------------- ------------ ------------------------------------------------
          2             1 /data/yashan/yasdb_data/cn-2-1/archive/arch_0_1.ARC                         1                  6394         4096 2025-06-20 05:39:38 
```

#### GV$ELECTION/V$ELECTION

Leader election view that contains information such as the current LEADER, term, synchronization log points, state, last heartbeat time, and node group members. For detailed field descriptions, please refer to [GV$ELECTION](../Reference Manual/System Views/Dynamic Performance Views/GV$ELECTION)/[V$ELECTION](../Reference Manual/System Views/Dynamic Performance Views/V$ELECTION).

***Example*** for Standalone Deployment

```sql
SELECT leader_group_id,leader_group_node_id,term,lfn,lfn_term,state,last_heartbeat_time,peers FROM V$ELECTION;
```

***Example*** for YAC Deployment

```sql
SELECT inst_id,leader_group_id,leader_group_node_id,term,lfn,lfn_term,state,last_heartbeat_time,peers FROM GV$ELECTION;
```

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT group_id,group_node_id,leader_group_id,leader_group_node_id,term,lfn,lfn_term,state,last_heartbeat_time,peers FROM GV$ELECTION;
```

### Unique Views for Primary Database

**GV$ARCHIVE_DEST_STATUS/V$ARCHIVE_DEST_STATUS**

Standby database list and status view that contains dest_id Standby database number, connection status, status of Standby database, received_lfn Standby database receiving point, and applied_lfn Standby database apply point, etc. For detailed field descriptions, please refer to [GV$ARCHIVE_DEST_STATUS](../Reference Manual/System Views/Dynamic Performance Views/GV$ARCHIVE_DEST_STATUS)/[V$ARCHIVE_DEST_STATUS](../Reference Manual/System Views/Dynamic Performance Views/V$ARCHIVE_DEST_STATUS).

In Standalone Deployment, this type of view can also be queried on the upper level standby database of cascade backups to obtain information about its subordinate cascade backup list.

***Example*** for Standalone Deployment and YAC Deployment

```sql
--  primary/standby deployment environment or primary/standby cluster deployment environment
SELECT dest_id,connection,status,database_mode,received_lfn,applied_lfn,synchronized FROM V$ARCHIVE_DEST_STATUS;
DEST_ID CONNECTION     STATUS  DATABASE_MODE  RECEIVED_LFN  APPLIED_LFN SYNCHRONIZED 
------- -------------- ------- -------------- ------------ ------------ ------------ 
      2 CONNECTED      NORMAL  OPEN                   1179         1179 YES         
      3 DISCONNECTED   UNKOWN  UNKOWN                  985          985 NO     
```

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Distributed high availability environment
SELECT dest_id,connection,status,database_mode,received_lfn,applied_lfn,synchronized FROM GV$ARCHIVE_DEST_STATUS WHERE GROUP_ID=2 AND GROUP_NODE_ID=3;
DEST_ID CONNECTION     STATUS  DATABASE_MODE  RECEIVED_LFN  APPLIED_LFN SYNCHRONIZED 
------- -------------- ------- -------------- ------------ ------------ ------------ 
      2 CONNECTED      NORMAL  OPEN                   1179         1179 YES         
      3 DISCONNECTED   UNKOWN  UNKOWN                  985          985 NO       
```

### Unique Views for Standby Database

#### GV$REPLICATION\_STATUS/V$REPLICATION\_STATUS

Standby database synchronization status view that contains status connection state, peer_point primary database log point, received_point Standby database receiving point, applied_point Standby database apply point, transport_lag log transfer delay time, and apply_lag log apply delay time, etc. For detailed field descriptions, please refer to [GV$REPLICATION_STATUS](../Reference Manual/System Views/Dynamic Performance Views/GV$REPLICATION_STATUS)/[V$REPLICATION_STATUS](../Reference Manual/System Views/Dynamic Performance Views/V$REPLICATION_STATUS).

***Example*** for Standalone Deployment and YAC Deployment

```sql
--  primary/standby deployment environment or primary/standby cluster deployment environment
SELECT connection,status,peer_addr,peer_point,received_point,applied_point,transport_lag,apply_lag FROM V$REPLICATION_STATUS;
CONNECTION        STATUS     PEER_ADDR               PEER_POINT     RECEIVED_POINT    APPLIED_POINT     TRANSPORT_LAG   APPLY_LAG
----------------- ---------- ----------------------- -------------- ----------------- ----------------- --------------- ------------
CONNECTED         NORMAL     192.168.4.201:1689      0-2-150        0-2-150           0-2-150           0               0
```

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Distributed high availability environment
SELECT group_id, group_node_id,connection,status,peer_addr,transport_lag,apply_lag FROM GV$REPLICATION_STATUS;
  GROUP_ID         GROUP_NODE_ID CONNECTION        STATUS            PEER_ADDR             TRANSPORT_LAG    APPLY_LAG 
---------- --------------------- ----------------- ----------------- --------------------- ------------- ------------ 
         2                     2 CONNECTED         NORMAL            127.0.0.1:1411                   0            0
         2                     3 CONNECTED         NORMAL            127.0.0.1:1411          2041932861            0
```

#### GV$RECOVERY\_PROGRESS/V$RECOVERY\_PROGRESS

Standby database apply progress view that contains Average Apply Rate, Redo Remain, Remain Time, and Parallelism information. For detailed field descriptions, please refer to [GV$RECOVERY_PROGRESS](../Reference Manual/System Views/Dynamic Performance Views/GV$RECOVERY_PROGRESS)/[V$RECOVERY_PROGRESS](../Reference Manual/System Views/Dynamic Performance Views/V$RECOVERY_PROGRESS).

This type of view can be used to analyze the performance of log apply on the standby database. For example, the Remain Time should typically be less than 1 second.

***Example*** for Standalone Deployment and YAC Deployment

```sql
--  primary/standby deployment environment or primary/standby cluster deployment environment
SELECT item,units,value FROM V$RECOVERY_PROGRESS;
ITEM                              UNITS                             VALUE 
--------------------------------- ----------------- --------------------- 
Active Apply Rate                 KB/sec                              337
Average Apply Rate                KB/sec                            22832
Maximum Apply Rate                KB/sec                           309180
Redo Applied                      MB                                    5
Redo Remain                       MB                                    0
First Applied Redo                LFN                                 987
Last Applied Redo                 LFN                                1180
Active Time                       Seconds                               0
Remain Time                       Seconds                               0
Non Physical Log Count            Number                                0
Parallelism                       Threads                               8
Latest Load Size                  KB                                    4
Latest Load Time                  Millisecond                           0
Latest Analysis Time              Millisecond                           0
Wait Previous Apply Time          Millisecond                          11
Latest Apply Time                 Millisecond                          11
```

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Distributed high availability environment
SELECT item, units, value FROM GV$RECOVERY_PROGRESS WHERE GROUP_ID=2 AND GROUP_NODE_ID=3;
ITEM                      UNITS              VALUE 
------------------------- ----------------- ------ 
Active Apply Rate         KB/sec              1843
Average Apply Rate        KB/sec              1843
Maximum Apply Rate        KB/sec              1843
Redo Applied              MB                     0
Redo Remain               MB                     0
First Applied Redo        LFN                    0
Last Applied Redo         LFN                 1022
Active Time               Seconds                0
Remain Time               Seconds                0
Non Physical Log Count    Number                 0
Parallelism               Threads                0
Latest Load Size          KB                     4
Latest Load Time          Millisecond            2
Latest Analysis Time      Millisecond            0
Wait Previous Apply Time  Millisecond            0
Latest Apply Time         Millisecond            2
```

## Monitoring Through *yasboot* Tool

Using the *yasboot* tool, the database status can be queried, where an instance_status field value of unknown or `-` indicates that the node status is abnormal and requires intervention.

::: tabs
==  Standalone Deployment 
```shell
$ yasboot cluster status -c yashandb -d
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0003 | db        | 1-3:3  | 49582 | open            | normal          | standby       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
```

==  YAC Deployment 

```shell
$ yasboot cluster status -c yashandb -d
+-------------------------------------------------------------------------------------------------------------------------------------------------+
+ hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address     | data_path                      |
|-------------------------------------------------------------------------------------------------------------------------------------------------+
+ host0001 | ce        | 1-1:1  | 19513 | open            | normal          | primary       | 192.168.1.2:1688   | /data/yashan/yasdb_data/ce-1-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0002 | ce        | 1-2:2  | 19608 | open            | normal          | primary       | 192.168.1.3:1688   | /data/yashan/yasdb_data/ce-1-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0003 | ce        | 2-1:3  | 19703 | open            | normal          | standby       | 192.168.10.4:1688   | /data/yashan/yasdb_data/ce-2-1 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
+ host0004 | ce        | 2-2:4  | 19651 | open            | normal          | standby       | 192.168.10.5:1688   | /data/yashan/yasdb_data/ce-2-2 |
|----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+--------------------------------+
```

==  ISC Distributed Cluster Deployment 

```shell
$ yasboot cluster status -c yashandb -d
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
:::
