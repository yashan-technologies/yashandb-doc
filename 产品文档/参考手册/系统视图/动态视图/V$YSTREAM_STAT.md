本视图显示YStream服务运行的统计信息。

| 字段  | 类型  | 说明 |
|-------|------|------|
|SERVER_ID        |INTEGER    |服务编号|                       
|SERVER_NAME      |VARCHAR(64)|服务名|                       
|START_TIME       |TIMESTAMP  |开始时间|                       
|STOP_TIME        |TIMESTAMP  |停止时间|                    
|CAPTURE_INSTANCE_ID      |INTEGER     |最后解析日志所属的节点编号|       
|CAPTURE_LFN      |BIGINT     |已解析的redo日志LFN|                     
|CAPTURE_SCN      |BIGINT     |已解析的redo日志SCN|                       
|CAPTURE_SCN_LOCAL_TIME  |TIMESTAMP     |已解析的redo日志SCN对应的本地时间| 
|SEND_SIZE        |BIGINT     |发送的LCR大小（单位：字节）|                       
|SEND_COUNT       |BIGINT     |发送的LCR数量|                       
|CAPTURE_SIZE     |BIGINT     |已解析的日志大小（单位：字节）|                       
|CAPTURE_COUNT    |BIGINT     |已解析的LCR数量|                       
|SPILL_SIZE       |BIGINT     |溢出事务LCR大小（单位：字节）|                       
|SPILL_COUNT      |BIGINT     |溢出事务LCR数量|                       
|REDO_READ_COUNT  |BIGINT     |Redo读取次数|                       
|REDO_SORT_COUNT  |BIGINT     |Redo排序次数|                       
|RECORD_DECODE_COUNT    |BIGINT     |LCR解码次数|                       
|RECORD_FETCH_COUNT     |BIGINT     |LCR读取次数|                 
|REDO_READ_TIME  |BIGINT     |Redo读取耗时（单位：微秒）|                       
|REDO_SORT_TIME  |BIGINT     |Redo排序耗时（单位：微秒）|                       
|RECORD_DECODE_TIME    |BIGINT     |LCR解码耗时（单位：微秒）|                       
|RECORD_FETCH_TIME     |BIGINT     |LCR读取耗时（单位：微秒）|        
|XACT_MEMORY_USED |BIGINT     |活跃事务消耗内存（单位：字节）|                       
|SPILL_MEMORY_USED|BIGINT     |溢出事务消耗（单位：字节）|                       
|DDL_MEMORY_USED  |BIGINT     |DDL消耗内存（单位：字节）|
