本视图显示集群中所有节点的备库redo传输汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| THREAD# | TINYINT | 实例编号 |
| CONNECTION | VARCHAR(16) | 主备连接状态<br>\*   CONNECTED：已连接<br>\*   DISCONNECTED：未连接 |
| STATUS | VARCHAR(16) | 日志同步状态<br>\*   NORMAL：正常<br>\*   NEED REPAIR：需要修复备库 |
| PEER\_ROLE | VARCHAR(16) | 对端数据库角色<br>\*   PRIMARY：主<br>\*   STANDBY：备<br>\*   UNKNOWN：未连接，角色未知 |
| PEER\_MODE | VARCHAR(16) | 对端数据库运行状态，即对端V$INSTANCE视图的STATUS字段 <br>\*   STARTED：数据库进程启动，此状态下不能操作数据库<br>\*   MOUNTED：数据库进程已经加载物理文件，此状态下能进行少量的维护操作<br>\*   OPEN：数据库正常运行状态<br>\*   UNKNOWN：未连接，状态未知 |
| PEER\_ADDR | VARCHAR(256) | 对端数据库的HA链路监听地址 |
| PEER\_POINT | VARCHAR(16) | 主库当前日志刷盘点，格式为{resetid}-{asn}-{blockid}  |
| PEER\_LFN | BIGINT | 主库当前日志序列号 |
| RECEIVED\_POINT | VARCHAR(16) | 备库当前日志接收点，格式为{resetid}-{asn}-{blockid}  |
| RECEIVED\_LFN | BIGINT | 备库当前日志接收序列号 |
| APPLIED\_POINT | VARCHAR(16) | 备库当前日志回放点，格式为{resetid}-{asn}-{blockid}  |
| APPLIED\_LFN | BIGINT | 备库当前日志回放序列号 |
| TRANSPORT\_LAG | INTEGER | 日志传输延迟时间（单位：毫秒） |
| APPLY\_LAG | INTEGER | 日志回放延迟时间（单位：毫秒） |
| APPLY\_FINISH\_TIME | INTEGER | 日志回放预估剩余时间（单位：毫秒） |
| GAP\_SEQ# | INTEGER | redo GAP 的最小日志序列号，0表示不存在redo GAP |
| ERROR | VARCHAR(512) | 该字段表示备库NEED REPAIR的错误原因 |
| TIME_SINCE_LAST_MSG | INTEGER | 从备库收到最后一条消息，到现在经过的时间（单位：秒） |
| PEER\_NODE\_ID | VARCHAR(16) | 对端数据库的NODEID |
| TRIGGER\_COND\_FAILOVER | VARCHAR(16) | 主库是否触发了条件故障切换 |



> **Note**: 
>
> {resetid}\_{asn}\_{blockid}
>
> - resetid：redo日志的reset id，每次重置redo时间线会使该值加1。
> - asn：归档序列号（archive sequence number），每产生一个redo，asn会加1，每个redo的asn不相同。
> - blockid：redo文件内页面所在ID，页面的偏移量为block id \* block size。

