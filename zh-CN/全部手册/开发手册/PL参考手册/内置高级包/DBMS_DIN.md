DBMS_DIN包提供了一组内置的存储过程，用于检测分布式内部网络节点级、链路级的响应性能。

> **Note**:
>
> - 调用DBMS_DIN高级包下的所有子程序时，都需要以SYS用户连接数据库，否则报错。
> - DBMS_DIN高级包仅支持存算一体分布式集群部署形态，在单机/共享集群/分布式集群形态下执行会报错。

## TRACE\_TRIGGER

```plsql
DBMS_DIN.TRACE_TRIGGER(
    NODE_ID        IN INTEGER DEFAULT -1,
    LINK_LEVEL     IN SMALLINT DEFAULT -1,
    LINK_ID        IN SMALLINT DEFAULT -1,
    PACK_SIZE    IN INTEGER DEFAULT 28);
```

TRACE_TRIGGER程序用于触发分布式内部网络进行链路级的响应速度探测，可通过入参指定探测的特定节点或链路以及探测时携带的额外数据包大小。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| NODE_ID   | 通讯节点ID，默认值-1，即默认向所有活跃节点发送探测 |
| LINK_LEVEL | 通讯通道等级，默认值-1，即默认向所有通道发送探测 |
| LINK_ID | 通讯链路ID，默认值-1，即默认向通道的所有活跃链路发送探测 |
| PACK_SIZE | 探测时携带的数据包大小（单位：字节），最大不超过32KB，不小不低于28字节 |

使用说明：

- 若指定NODE_ID，则仅探测指定节点，不可指定自身或非法节点。
- 若指定LINK_LEVEL和LINK_ID，则仅探测指定通道的指定链路，LEVEL 0~2分别对应内部通道（INNER）、控制通道（CONTROL）、数据通道（DATA）。
- NODE_ID，LINK_LEVEL和LINK_ID可根据视图[V\$DIN_LINK](../../../参考手册/系统视图/动态视图/V$DIN_LINK)和[GV$DIN_LINK](../../../参考手册/系统视图/动态视图/GV$DIN_LINK)的查询结果进行参考。
- 接收链路不具备探测能力，既不会执行也不会报错。

示例（存算一体分布式集群部署）

```plsql
-- 不指定探测节点、链路以及数据包大小，使用默认值
EXEC DBMS_DIN.TRACE_TRIGGER();
-- 指定数据包大小
EXEC DBMS_DIN.TRACE_TRIGGER(
    PACK_SIZE => 1024);
-- 指定探测特定节点
EXEC DBMS_DIN.TRACE_TRIGGER(
    NODE_ID => 3);
-- 指定探测特定节点的特定链路
EXEC DBMS_DIN.TRACE_TRIGGER(
    NODE_ID => 3,
    LINK_LEVEL => 1,
    LINK_ID => 1);
```
