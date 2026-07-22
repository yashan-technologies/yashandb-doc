本视图显示当前statement上次执行的并行查询的表队列的统计信息，可用于分析表队列分片是否合理，只在连接存续期间可以查询。

|  字段| 类型| 说明|
| ---                     | ---         | --- |
| STAGE\_ID               | SMALLINT    | 所处的stage ID |
| TQ\_ID                  | SMALLINT    | 表队列ID |
| SERVER\_TYPE            | VARCHAR(8)  | 表队列类型<br>\* PRODUCER<br>\* CONSUMER |
| SLICE\_ID               | SMALLINT    | 表队列根据worker数量划分的分片 |
| WORKER\_ID              | INTEGER    | 负责该分片的worker的ID，如果是COORDINATOR，则为-1 |
| NUM\_ROWS               | BIGINT      | 表队列分片处理的行数 |
| BYTES                   | BIGINT      | 表队列分片处理的字节数 |
| OPEN\_TIME              | INTEGER     | 表队列分片的持续时间（单位：毫秒） |
| AVG\_LATENCY            | INTEGER     | 数据在队列中等待的平均延迟时间（单位：毫秒） |
| WAITS                   | INTEGER     | CONSUMER从队列分片获取数据或PRODUCER往队列分片推数据的等待次数 |
| TIMEOUTS                | INTEGER     | CONSUMER从队列分片获取数据或PRODUCER往队列分片推数据的超时次数 |
