本视图显示日志回放状态的信息。

|  字段| 类型| 说明|
| ----- | ----------- | ------------ |
| THREAD#  | TINYINT | 数据库的实例编号 |
| STATUS | VARCHAR(16) | 数据库的回放状态<br>\* NONE：当前数据库无需回放日志<br>\* RUNNING：正在回放日志<br>\* PAUSED：已暂停回放日志<br>\* IDLE：当前数据库回放线程处于空闲状态|
| PARALLELISM | SMALLINT | 并行回放线程数，不开启并行回放时值为0，开启时只能为2的幂次方 |
| START\_RECOVERY\_TIME | DATE      | 启动回放的时间戳 |
| STOP\_RECOVERY\_TIME | DATE      | 停止回放的时间戳 |
| REPLAY\_POINT | VARCHAR(32)      | 当前的日志回放点，格式为{resetid}-{asn}-{blockid}-{lfn}  |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid：redo日志的reset id，每次重置redo时间线会使该值加1。
> - asn：归档序列号（Archive Sequence Number），每产生一个redo，asn会加1，每个redo的asn不相同。
> - blockid：redo文件内页面所在ID，页面的偏移量为block id \* block size。
> - lfn：日志序列号（Log Flush Number），每次redo刷盘，lfn加1。

