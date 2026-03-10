本视图显示逻辑备库的回放进度。

| 字段  | 类型  | 说明 |
|-------|------|------|
|APPLIED_SCN  |BIGINT     |所有COMMIT SCN小于或等于此SCN的事务均已应用|
|APPLIED_TIME |TIMESTAMP  |APPLIED_SCN对应的时间戳|
|APPLIED_LSN  |BIGINT     |已经应用事务的日志最大LSN  |
|APPLIED_POINT|VARCHAR(64)|当前的日志回放点，格式{rst}_{asn}_{blockid}_{lfn}|
|RESTART_SCN  |BIGINT     |服务重启之后，YSTREAM不会读取任何低于此SCN的日志文件|
|RESTART_TIME |TIMESTAMP  |RESTART_SCN对应的时间戳|
|RESTART_LSN  |BIGINT     |服务重启之后，YSTREAM开始解析的日志起始LSN|
|CAPTURE_SCN  |BIGINT     |当前已解析的日志SCN|
|CAPTURE_TIME |TIMESTAMP  |CAPTURE_SCN对应的时间戳|
|CAPTURE_LSN  |BIGINT     |YSTREAM已解析的日志LSN|
|RECEIVE_SCN  |BIGINT     |备库当前的日志接受SCN|
|RECEIVE_TIME |TIMESTAMP  |RECEIVE_SCN对应的时间戳|
|RECEIVE_LSN  |BIGINT     |备库当前的日志接受LSN|
|RECEIVE_POINT|VARCHAR(64)|备库当前的日志接收点，格式{rst}_{asn}_{blockid}_{lfn} |

> **Note**：
>
> {rst}_{asn}_{blockid}_{lfn}
>
> rst：为reset id，每次failover后，数据库新产生的redo文件的reset id会加1。
>
> asn: 归档序列号，archive sequence number，每产生一个redo，ASN会加1，每个redo的ASN不相同。
>
> blockid：redo文件内页面所在ID，页面的偏移量为 block id\*block size。
>
> lfn：log flush number，日志序列号，每次redo刷盘，LFN加1。