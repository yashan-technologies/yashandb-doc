## 功能简介

yacConnect函数用于客户端发起连接请求，返回YAC_SUCCESS表示连接成功，返回YAC_ERROR表示连接失败。

## 函数声明

```c
yacResult yacConnect ( yacHandle hConn,
                       const yacChar* url,
                      YacInt16 urlLength,
                      const yacChar* user,
                      YacInt16 userLength,
                      const yacChar* password,
                      YacInt16 passwordLength);
```

## 参数说明

|  参数名| 说明|
| ------------------- |---------------------------------------------------------|
| hConn (IN/OUT)      | 连接信息句柄。                   |
| url (IN)            | 目标数据库的URL，支持通过单地址、多地址或多地址组连接，详细介绍请查阅下文。 |
| urlLength (IN)      | 服务端地址长度，可传入YAC_NULL_TERM_STR表示以\0结尾。                     |
| user (IN)           | 用户名。                                  |
| userLength (IN)     | 用户名长度，可传入YAC_NULL_TERM_STR表示以\0结尾。             |
| password (IN)       | 用户密码。              |
| passwordLength (IN) | 用户密码长度，可传入YAC_NULL_TERM_STR表示以\0结尾。           |

URL格式如下：

* 单地址连接： `host:port[/pdb_name]`。

* 多个地址连接：`serverType:host:port,host:port,host:port,host:port[/pdb_name]`，多个地址间用`,`分隔，连接时根据服务类型（serverType参数）配置对相应节点进行连接。

* 多组地址连接：`serverType:host:port,host:port;host:port,host:port[/pdb_name]`，多组地址间用`;`分隔，同组内的多个地址间用`,`分隔，连接时先在组内根据serverType配置对相应节点进行连接，当组内所有连接均失败后按顺序优先级（越靠前优先级越高）访问下一组。

参数含义：

* host:port[/pdb_name]：

    * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../../../../数据库管理/集群管理/SCAN管理)或[VIP](../../../../../数据库管理/集群管理/VIP管理)，还可以使用相应的域名或IP地址。

    * port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。
    * pdb_name：仅用于容器数据库，指定连接到具体某个PDB，省略则默认连接至根容器。

* serverType：多地址连接的连接类型，可选项包括primary、standby、loadBalance、primaryLoadBalance以及standbyLoadBalance。若不指定serverType，在输入多IP时默认采用primary。各类型的详细介绍如下：

    | serverType| 说明|
    |--------------------|---------------|
    | primary | 默认类型，可省略。<br />驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与主节点建立的连接。 |
    | standby | 驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与备节点建立的连接。 |
    | loadBalance | 驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |
    |primaryLoadBalance | 驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数量和角色，选取主节点中会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |
    |standbyLoadBalance | 驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数量和角色，选取备节点中会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |





>**Note**:
>
> - 多组监听地址连接的故障切换效率低于多个监听地址连接，分组主要用于尽可能保障仅通过第一组地址建立数据库连接，请根据实际需求选择是否分组。
> - 在共享集群/分布式集群部署中，若已配置[SCAN](../../../../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../../../../数据库管理/集群管理/VIP管理.md)，直接使用数据库服务端提供的高可用能力即可，**无需**额外配置多地址连接。
> - 配置多地址连接共享集群/分布式集群时：
>   - 若为单集群部署，驱动会将其所有实例视作主节点。
>   - 若为主备集群部署，驱动会将主集群的所有实例视作主节点、备集群的所有实例视作备节点。如需在未配置SCAN或VIP的场景下实现负载均衡，可考虑配置多组地址连接并按需指定primaryLoadBalance或standbyLoadBalance，主集群的所有实例为一个组，备集群的所有实例为另一个组。



