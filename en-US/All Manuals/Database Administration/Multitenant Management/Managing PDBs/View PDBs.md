PDB-related information can be queried using the following methods:

| Methods   | Description                 |
| -------------------------------- | -------------------------------------------- |
| [GV$CONTAINERS](../../../Reference Manual/System Views/Dynamic Performance Views/GV$CONTAINERS)/[V$CONTAINERS](../../../Reference Manual/System Views/Dynamic Performance Views/V$CONTAINERS)                | Can view container ID, name, container type, running status, data file storage path, and syntax mode for all containers (including the CDB root, PDB seed, and all PDBs). |
| [GV$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/GV$PDBS)/[V$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/V$PDBS)                                       | Can view container ID, name, container type, running status, data file storage path, and syntax mode for all PDBs (including PDB seed). |
|  [*yasql*](../../../Tools Guide/yasql/User Guide for yasql.html#show_pdbs)         | Can view container ID, name, and running status for all PDBs (including PDB seed). |
| [*yasboot*](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot pdb) | Can view the running status, primary/standby role, and listening address information for the specified PDB. |
| [*ycsctl*](../../../Tools Guide/ycsctl/Node Management Commands)                                                   | Only applicable to the CDB in YAC/Distributed Cluster Deployment.  <br/>Can view the running status and primary/standby role information for the specified PDB. |

## Through Dynamic Views

On the CDB root, corresponding dynamic views can be used to obtain basic information, running status, and syntax mode information of all PDBs in the current environment.

- [GV$CONTAINERS](../../../Reference Manual/System Views/Dynamic Performance Views/GV$CONTAINERS)/[V$CONTAINERS](../../../Reference Manual/System Views/Dynamic Performance Views/V$CONTAINERS): Can view information of the CDB root and all PDBs, including container ID, container name, container type, running status, data file storage path, and syntax mode, etc.

- [GV$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/GV$PDBS)/[V$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/V$PDBS): Can view information of all PDBs, including container ID, container name, container type, running status, data file storage path, and syntax mode, etc.

If the above views are queried by directly connecting to a PDB, only the corresponding information of the current PDB can be obtained.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Query the status of all containers (including CDB$ROOT)
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN

-- Query the status of all PDBs
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN
```

## Using *yasql*


PDB information can be viewed through `show pdbs`, including container ID, container name, and running status.  

- Connect to the CDB root and execute this command to query information of all PDBs in the current environment (including the built-in PDB seed).  

- Directly connect to a specific PDB and execute this command to query information of the current PDB.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Connect to the CDB root and execute this command
$ yasql c##sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN

SQL> exit

# Directly connect to pdb1 and execute this command
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    2 PDB1                                                             OPEN
```


## Using *yasboot*

The yasboot pdb status command can be used to view information of specified PDBs, including PDB node distribution, PDB name, running status, primary-standby role, and listening address, etc.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasboot pdb status -c yashandb --pdb pdb1
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | nodeid | node_type | pdb_name         | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address   | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | 1-1:1  | cdb       | pdb1   | 9560  | open            | open       | primary  | -           | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+--------+-----------+------------------+-------+-----------------+------------+----------+-------------+--------------------+------------------------------+
| host0002 | 1-2:2  | cdb       | pdb1   | 10949 | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+--------+-----------+------------------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
```

## Using *ycsctl* 

In a CDB deployed in YAC/distributed cluster, the ycsctl status pdb command can be used to view information of specified PDBs, including PDB node distribution, PDB name, running status, and primary-standby role, etc.

***Example*** for YAC/Distributed Cluster Deployment

```shell
$ ycsctl status pdb -db yashandb -pdb pdb1
+-------------------+---------------------------------+---------------------------+-------------------------------------+
|   Self Host ID    |        Cluster Master ID        |      YasFS Master ID      |          Active Host Count          |
+-------------------+---------------------------------+---------------------------+-------------------------------------+
|1                  |1                                |1                          |2                                    |
+-------------------+---------------------------------+---------------------------+-------------------------------------+
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
| Host ID | Target  |   YCS   |   YFS   |     VIP     |      DB_NAME       |      PDB_NAME      | DB_STATE  | IS_MASTER |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|1        |online   |online   |online   |             |yashandb            |pdb1                        |online     |TRUE       |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|2        |online   |online   |online   |             |yashandb            |pdb1                        |online     |FALSE      |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
```
