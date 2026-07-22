本视图显示分支数据库环境中各个分支的资源使用情况。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| BRANCH_NAME               | VARCHAR(68) | 分支名称 |
| CPU_WAIT_TIME             | BIGINT      | 因资源管理而等待CPU的累计时间（单位：毫秒） |
| CPU_WAITS                 | BIGINT      | 因资源管理等待CPU的累计次数 |
| CONSUMED_CPU_TIME         | BIGINT      | 所有会话累计消耗的CPU时间（单位：毫秒） |
| SHARED_POOL_BYTES         | BIGINT      | 内存共享池的使用量（单位：bytes） |
| SPA_BYTES                 | BIGINT      | 分支中SPA内存使用量（单位：bytes） |
| MEMORY_USED_BYTES         | BIGINT      | 分支当前使用的内存大小（单位：bytes）|
| MEMORY_LIMIT_BYTES        | BIGINT      | 分支可用内存上限（单位: bytes）|
| BRANCH_ID                 | BIGINT      | 分支ID |
