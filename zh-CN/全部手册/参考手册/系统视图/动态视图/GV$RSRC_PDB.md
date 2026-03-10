本视图显示容器数据库环境中各个容器的资源使用情况。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | 组ID               |
| GROUP_NODE_ID | NUMBER       | 组内节点ID                |
| INST_ID       | NUMBER       | 实例ID                   |
| PDB_NAME                  | VARCHAR(68) | 容器名称 |
| CPU_WAIT_TIME             | BIGINT      | 因资源管理而等待CPU的累计时间（单位：毫秒） |
| CPU_WAITS                 | BIGINT      | 因资源管理等待CPU的累计次数 |
| CONSUMED_CPU_TIME         | BIGINT      | 所有会话累计消耗的CPU时间（单位：毫秒） |
| YIELDS                    | BIGINT      | 因为达到时间片而将CPU退让出来的累计次数，目前值固定为`NULL` |
| IO_SERVICE_TIME           | BIGINT      | 累计I/O等待时间，目前值固定为`NULL` |
| IO_SERVICE_WAITS          | BIGINT      | 累计I/O等待次数，目前值固定为`NULL` |
| SMALL_READ_MEGABYTES      | BIGINT      | 读取的单块字节数，目前值固定为`NULL` |
| SMALL_WRITE_MEGABYTES     | BIGINT      | 写入的单块字节数，目前值固定为`NULL` |
| LARGE_READ_MEGABYTES      | BIGINT      | 读取的多块字节数，目前值固定为`NULL` |
| LARGE_WRITE_MEGABYTES     | BIGINT      | 写入的多块字节数，目前值固定为`NULL` |
| SMALL_READ_REQUESTS       | BIGINT      | 单块读取请求次数，目前值固定为`NULL` |
| SMALL_WRITE_REQUESTS      | BIGINT      | 单块写入请求次数，目前值固定为`NULL` |
| LARGE_READ_REQUESTS       | BIGINT      | 多块读取请求次数，目前值固定为`NULL` |
| LARGE_WRITE_REQUESTS      | BIGINT      | 多块写入请求次数，目前值固定为`NULL` |
| PQS_COMPLETED             | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| PQ_SERVERS_USED           | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| PQS_QUEUED                | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| PQ_ACTIVE_TIME            | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| PQ_QUEUED_TIME            | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| PQ_QUEUE_TIME_OUTS        | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| CURRENT_PQS_ACTIVE        | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| CURRENT_PQ_SERVERS_ACTIVE | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| CURRENT_PQS_QUEUED        | BIGINT      | 仅用于兼容，目前值固定为`NULL` |
| SGA_BYTES                 | BIGINT      | 容器中PGA内存使用量（单位：bytes），目前值固定为`NULL` |
| BUFFER_CACHE_BYTES        | BIGINT      | buffer_cache内存使用量（单位：bytes） ，目前值固定为`NULL` |
| SHARED_POOL_BYTES         | BIGINT      | shared_pool内存使用量（单位：bytes） |
| SPA_BYTES                 | BIGINT      | 容器中SPA内存使用量（单位：bytes） |
| CON_ID                    | BIGINT      | 容器ID |
