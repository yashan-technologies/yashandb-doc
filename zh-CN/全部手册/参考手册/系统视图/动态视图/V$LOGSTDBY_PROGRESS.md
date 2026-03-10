本视图显示逻辑备库的回放进度。

|  字段| 类型| 说明|
|-------|------|------|
|APPLIED_SCN  |BIGINT     |所有COMMIT SCN小于或等于此SCN的事务均已应用|
|APPLIED_TIME |TIMESTAMP(6)  |APPLIED_SCN对应的时间戳|
|APPLIED_LSN  |BIGINT     |已经应用事务的日志最大LSN  |
|APPLIED_POINT|VARCHAR(64)|当前的日志回放点，格式为{resetid}-{asn}-{blockid}-{lfn} |
|RESTART_SCN  |BIGINT     |服务重启之后，YStream不会读取任何低于此SCN的日志文件|
|RESTART_TIME |TIMESTAMP(6)  |RESTART_SCN对应的时间戳|
|RESTART_LSN  |BIGINT     |服务重启之后，YStream开始解析的日志起始LSN|
|CAPTURE_SCN  |BIGINT     |当前已解析的日志SCN|
|CAPTURE_TIME |TIMESTAMP(6)  |CAPTURE_SCN对应的时间戳|
|CAPTURE_LSN  |BIGINT     |YStream已解析的日志LSN|
|RECEIVE_SCN  |BIGINT     |备库当前的日志接受SCN|
|RECEIVE_TIME |TIMESTAMP(6)  |RECEIVE_SCN对应的时间戳|
|RECEIVE_LSN  |BIGINT     |备库当前的日志接受LSN|
|RECEIVE_POINT|VARCHAR(64)|备库当前的日志接收点，格式为{resetid}-{asn}-{blockid}-{lfn}  |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid：redo日志的reset id，每次重置redo时间线会使该值加1。
> - asn：归档序列号（Archive Sequence Number），每产生一个redo，ASN会加1，每个redo的ASN不相同。
> - blockid：redo文件内页面所在ID，页面的偏移量为block id \* block size。
> - lfn：日志序列号（Log Flush Number），每次redo刷盘，LFN加1。

