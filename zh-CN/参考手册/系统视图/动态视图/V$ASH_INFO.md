本视图用于显示ASH缓冲区的相关统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TOTAL_SIZE                | BIGINT      | ASH缓存区总大小，单位字节                                                |
| FIXED_SIZE                | BIGINT      | ASH缓存区固定大小，单位字节                                                |
| SAMPLING_INTERVAL         | BIGINT      | 采样间隔时间，单位毫秒                                                |
| OLDEST_SAMPLE_ID          | BIGINT      | 缓冲区中最老样本ID                                                |
| OLDEST_SAMPLE_TIME        | TIMESTAMP(6)   | 最老样本时间戳                                                |
| LATEST_SAMPLE_ID          | BIGINT      | 最新样本ID                                                |
| LATEST_SAMPLE_TIME        | TIMESTAMP(6)   | 最新样本时间戳                                                |
| SAMPLE_COUNT              | BIGINT      | 采样总次数                                                |
| DROPPED_SAMPLE_COUNT      | BIGINT      | 丢弃的样本数                                                |
| SAMPLED_BYTES             | BIGINT      | 已采样的数据总大小，单位字节                                                |
| SAMPLER_ELAPSED_TIME      | BIGINT      | 采样总耗时，单位微秒                                                |
| DISK_FILTER_RATIO         | INTEGER     | 内存样本数和磁盘样本数的比率                                                |
| LATEST_FLUSH_SAMPLE_ID    | BIGINT      | 最新刷盘样本ID                                                 |
| AWR_FLUSH_BYTES           | BIGINT      | 已刷盘数据大小，单位字节                                                |
| AWR_FLUSH_ELAPSED_TIME    | BIGINT      | 刷盘总耗时，单位微秒                                                |
| AWR_FLUSH_COUNT           | BIGINT      | 刷盘总次数                                                |
| AWR_FLUSH_EMERGENCY_COUNT | BIGINT      | 紧急刷盘次数                                                |
| CON_ID                    | INTEGER     | 当前租户ID（保留字段）                                                |
