本视图显示日志回放状态的信息。

| 字段  | 类型        | 说明         |
| ----- | ----------- | ------------ |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| THREAD#  | TINYINT | 数据库的实例编号 |
| STATUS | VARCHAR(16) | 数据库的回放状态<br>\* NONE：当前数据库无需回放日志<br>\* RUNNING：正在回放日志<br>\* PAUSED：已暂停回放日志<br>\* ERROR：当前数据库回放日志出现了故障|
| PARALLELISM | SMALLINT | 并行回放线程数，不开启并行回放时值为0，开启时只能为2的幂次方 |
| START_RECOVERY_TIME | DATE      | 启动回放的时间戳 |
| STOP_RECOVERY_TIME | DATE      | 停止回放的时间戳 |
| REPLAY_POINT | VARCHAR(32)      | 当前的日志回放点，格式{rst}_{asn}_{blockid}_{lfn} |

> **Note**：
>
> {rst}_{asn}_{blockid}_{lfn}
>
> rst：为reset id，每次failover后，数据库新产生的redo文件的reset id会加1。
>
> asn：归档序列号，archive sequence number，每产生一个redo，ASN会加1，每个redo的ASN不相同。
>
> blockid：redo文件内页面所在ID，页面的偏移量为 block id\*block size。
>
> lfn：log flush number，日志序列号，每次redo刷盘，LFN加1。

