本视图显示集群中所有节点的备库redo传输汇总信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| THREAD# | TINYINT | 实例编号 |
| CONNECTION | VARCHAR(16) | 主备连接状态<br>\*   CONNECTED：已连接<br>\*   DISCONNECTED：未连接 |
| STATUS | VARCHAR(16) | 日志同步状态<br>\*   NORMAL：正常<br>\*   NEED REPAIR：需要修复备库 |
| PEER_ROLE | VARCHAR(16) | 对端数据库角色<br>\*   PRIMARY：主<br>\*   STANDBY：备<br>\*   UNKNOWN：未连接，角色未知 |
| PEER_MODE | VARCHAR(16) | 对端数据库运行状态，即对端V$INSTANCE视图的STATUS字段 <br>\*   STARTED：数据库进程启动，此状态下不能操作数据库<br>\*   MOUNTED：数据库进程已经加载物理文件，此状态下能进行少量的维护操作<br>\*   OPEN：数据库正常运行状态<br>\*   UNKNOWN：未连接，状态未知 |
| PEER_ADDR | VARCHAR(256) | 对端数据库的HA链路监听地址 |
| PEER_POINT | VARCHAR(16) | 主库当前日志刷盘点。格式{rst}_{asn}_{blockid} |
| PEER_LFN | BIGINT | 主库当前日志序列号 |
| RECEIVED_POINT | VARCHAR(16) | 备库当前日志接收点。格式{rst}_{asn}_{blockid} |
| RECEIVED_LFN | BIGINT | 备库当前日志接收序列号 |
| APPLIED_POINT | VARCHAR(16) | 备库当前日志回放点。格式{rst}_{asn}_{blockid} |
| APPLIED_LFN | BIGINT | 备库当前日志回放序列号 |
| TRANSPORT_LAG | INTEGER | 日志传输延迟时间（单位：毫秒） |
| APPLY_LAG | INTEGER | 日志回放延迟时间（单位：毫秒） |
| APPLY_FINISH_TIME | INTEGER | 日志回放预估剩余时间（单位：毫秒） |
| GAP_SEQ# | INTEGER | redo GAP 的最小日志序列号，0表示不存在redo GAP |
| ERROR | VARCHAR(512) | 该字段表示备库NEED REPAIR的错误原因 |
| TIME_SINCE_LAST_MSG | INTEGER | 从备库收到最后一条消息，到现在经过的时间（单位：秒） |
| PEER_NODE_ID | VARCHAR(16) | 对端数据库的NODEID |
| TRIGGER_COND_FAILOVER | VARCHAR(16) | 主库是否触发了条件故障切换 |

> **Note**：
>
> {rst}_{asn}_{blockid}
>
> rst：为reset id，每次failover后，数据库新产生的redo文件的reset id会加1。
>
> asn: 归档序列号，archive sequence number，每产生一个redo，ASN会加1，每个redo的ASN不相同。
>
> blockid：redo文件内页面所在ID，页面的偏移量为 block id\*block size。
>
> lfn：log flush number，日志序列号，每次redo刷盘，LFN加1。