本视图显示在HA架构中开启自动选主时，当前节点实时的选举状态，当自动选主关闭时本视图无数据。

|  字段| 类型| 说明|
| --- | --- | --- |
| LEADER\_GROUP\_ID | INTEGER | 主节组ID |
| LEADER\_GROUP\_NODE\_ID | INTEGER | 主节点的节点ID |
| TERM | BIGINT | 当前主节点的任期 |
| LFN | BIGINT | 日志刷盘序号 |
| LFN\_TERM | BIGINT | 日志刷盘的任期 |
| STATE | VARCHAR(64) | 当前节点的选举状态<br>\*   Startup：启动<br>\*   PreCandidate：预选举<br>\*   Candidate：候选者<br>\*   Follower：跟随者<br>\*   Leader：领导者<br>\*   Shutdown：关闭<br>\*   Unknown：未知 |
| LAST\_HEARTBEAT\_TIME | TIMESTAMP(6) | 当前节点最后一次收到心跳的时间 |
| PEERS | VARCHAR(768) | 当前节点的节点组成员 |
| EXTEND_INFO | JSON | 组内所有节点的补充信息 |
