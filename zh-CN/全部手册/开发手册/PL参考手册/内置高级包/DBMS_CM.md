DBMS_CM包提供了一组内置的存储过程/函数，用于yasboot在特定功能场景中调用维护分布式集群的元数据信息。

> **Caution**:
>
> - 不建议用户直接调用DBMS_CM高级包，避免造成不可控影响。
>
> - DBMS_CM高级包仅适用于存算一体分布式集群部署。

## CREATE\_CLUSTER

```plsql
DBMS_CM.CREATE_CLUSTER(
   CLUSTER_ID   IN VARCHAR,
   CLUSTER_NAME IN VARCHAR,
   MN_HOST      IN VARCHAR
   );
```

该函数用于创建分布式集群的集群级别元数据信息，相应信息将被写入缓存和系统表中。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|CLUSTER_ID|分布式集群的唯一UUID，部署时由yasboot自动生成。|
|CLUSTER_NAME|分布式集群的名称。|
|MN_HOST|首次部署时首个MN节点的服务器地址。|

## UPDATE\_CLUSTER\_ID

```plsql
DBMS_CM.UPDATE_CLUSTER_ID();
```

该函数用于更新分布式集群的集群级别元数据信息，将相应信息从配置参数文件中加载到内存和系统表。

## CREATE\_GROUP

```plsql
DBMS_CM.CREATE_GROUP(
   GROUP_ID   OUT INT,
   GROUP_NAME IN VARCHAR,
   GROUP_TYPE IN VARCHAR
   );
```

该函数用于创建分布式集群的节点组级别元数据信息，相应信息将被写入缓存和系统表中。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_ID|节点组ID。|
|GROUP_NAME|节点组名称。|
|GROUP_TYPE|节点组类型，主要包含：CN（协调节点），DN（数据节点），MN（管理节点）。|

## DELETE\_GROUP

```plsql
DBMS_CM.DELETE_GROUP(
   GROUP_ID   IN INT,
   IS_FORCE   IN BOOL
   );
```

该函数用于从系统表和缓存中删除分布式集群的节点组级别元数据信息。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_ID|节点组ID。|
|IS_FORCE|若指定节点组为该类型中最后/唯一的一个节点组，是否仍强制删除该组。|

## CREATE\_NODE

```plsql
DBMS_CM.CREATE_NODE(
   GROUP_NODE_ID    OUT INT,
   ENDPOINT         OUT INT,
   GROUP_ID         IN  INT,
   NODE_TYPE        IN  VARCHAR,
   HOST             IN  VARCHAR,
   SERVICE_ADDR     IN  VARCHAR,
   REPLICA_ADDR     IN  VARCHAR,
   DATA_ADDR        IN  VARCHAR,
   IS_SYNC_METADATA IN  BOOL
   );
```

该函数用于创建分布式集群的节点级别元数据信息，相关信息将被写入缓存和系统表中。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_NODE_ID|组内节点ID。|
|ENDPOINT|节点endpoint。|
|GROUP_ID|节点组ID。|
|NODE_TYPE|节点类型。|
|HOST|节点的服务器地址。|
|SERVICE_ADDR|节点的服务地址。|
|REPLICA_ADDR|节点的复制地址。|
|DATA_ADDR|节点的数据传输地址。|
|IS_SYNC_METADATA|是否需要同步DN组首节点或新扩容的CN节点的元数据信息。|

## DELETE\_NODE

```plsql
DBMS_CM.DELETE_NODE(
   GROUP_ID      IN INT,
   GROUP_NODE_ID IN INT
   );
```

该函数用于从缓存和系统表中删除分布式集群的节点级别元数据信息。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_ID|节点组ID。|
|GROUP_NODE_ID|组内的节点ID。|

## UPDATE\_DATA\_URL

```plsql
DBMS_CM.UPDATE_DATA_URL(
   GROUP_ID      IN INT,
   GROUP_NODE_ID IN INT,
   URL           IN VARCHAR,
   DATA_ADDR     IN VARCHAR,
   REPLICA_ADDR  IN VARCHAR,
   DATA_PATH     IN VARCHAR,
   HOST          IN VARCHAR
   );
```

该函数用于更新分布式集群中节点的URL信息以及数据路径，相关信息将被写入缓存和系统表中。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_ID|节点组ID。|
|GROUP_NODE_ID|组内节点ID。|
|SERVICE_ADDR|服务地址。|
|DATA_ADDR|数据地址。|
|REPLICA_ADDR|复制地址。|
|DATA_PATH|数据路径。|
|HOST|服务器地址。|

## UPDATE\_DATA\_PATH

```plsql
DBMS_CM.UPDATE_DATA_URL(
   GROUP_ID      IN INT,
   GROUP_NODE_ID IN INT,
   DATA_PATH     IN VARCHAR
   );
```

该函数用于更新分布式集群中节点的数据路径，相关信息将被写入缓存和系统表中。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|GROUP_ID|节点组ID。|
|GROUP_NODE_ID|组内节点ID。|
|DATA_PATH|节点数据路径。|

## TRIGGER\_PUSH

```plsql
DBMS_CM.TRIGGER_PUSH();
```

该函数用于分布式集群级别元数据不同步时，主动触发同步机制。
