PDB的相关信息可以使用以下方法进行查询：

| 方法 | 功能说明 |
| -------------------------------- | -------------------------------------------- |
| [GV$CONTAINERS](../../../参考手册/系统视图/动态视图/GV$CONTAINERS.md)/[V$CONTAINERS](../../../参考手册/系统视图/动态视图/V$CONTAINERS.md)视图 | 可以查看所有容器（含根容器、种子容器以及所有PDB）的容器ID、容器名称、容器类型、运行状态、数据文件存放路径以及语法模式等。 |
| [GV$PDBS](../../../参考手册/系统视图/动态视图/GV$PDBS)/[V$PDBS](../../../参考手册/系统视图/动态视图/V$PDBS)视图 | 可以查看所有PDB（含种子容器）的容器ID、容器名称、容器类型、运行状态、数据文件存放路径以及语法模式等。 |
| [yasql工具](../../../工具手册/yasql/yasql使用指导.md#show_pdbs)            | 可以查看所有PDB（含种子容器）的容器ID、名称以及运行状态。 |
| [yasboot工具](../../../工具手册/yasboot/yasboot命令介绍/yasboot pdb.md) | 可以查看指定PDB的运行状态、主备角色以及监听地址等信息。 |
| [ycsctl工具](../../../工具手册/ycsctl/ycsctl使用指导/数据库资源管理命令.md)                                               | 仅适用于共享集群/分布式集群部署的容器数据库。<br />可以查看指定PDB的运行状态以及主备角色等信息。 |

## 通过动态视图

在根容器上可以通过相应的动态视图获取当前环境所有PDB的基础信息、运行状态以及语法模式等信息。

- [GV$CONTAINERS](../../../参考手册/系统视图/动态视图/GV$CONTAINERS.md)/[V$CONTAINERS](../../../参考手册/系统视图/动态视图/V$CONTAINERS.md)：可以查看根容器和所有PDB的信息，包括容器ID、容器名称、容器类型、运行状态、数据文件存放路径以及语法模式等。

- [GV$PDBS](../../../参考手册/系统视图/动态视图/GV$PDBS)/[V$PDBS](../../../参考手册/系统视图/动态视图/V$PDBS)：可以查看所有PDB的信息，包括容器ID、容器名称、容器类型、运行状态、数据文件存放路径以及语法模式等。

若直连PDB查询上述视图则只能获取当前PDB的对应信息。

示例（单机/共享集群/分布式集群部署）

```sql
-- 查询所有容器的状态（包括CDB$ROOT)
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN

-- 查询所有PDB的状态
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN
```

## 使用yasql工具


通过show pdbs命令可以查看PBD信息，包括容器ID、PDB名称以及运行状态。  

- 连接根容器执行该命令，查询当前环境中所有PDB（含种子容器）的信息。  

- 直连某个PDB执行该命令，查询当前PDB的信息。  

示例（单机/共享集群/分布式集群部署）

```shell
# 连接根容器执行该命令
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

# 直连PDB执行该命令
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    2 PDB1                                                             OPEN
```


## 使用yasboot工具

通过yasboot pdb status命令可以查看指定PDB的信息，包括PDB所在节点分布、PDB名称、运行状态、主备角色以及监听地址等。

示例（单机/共享集群/分布式集群部署）

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

## 使用ycsctl工具

在共享集群/分布式集群部署的容器数据库中，通过ycsctl status pdb命令可以查看指定PDB的信息，包括PDB所在节点分布、PDB名称、运行状态以及主备角色等。

示例（共享集群/分布式集群部署）

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
