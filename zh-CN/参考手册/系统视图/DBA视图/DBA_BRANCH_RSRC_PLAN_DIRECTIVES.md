本视图显示所有分支数据库资源计划指令信息。

仅当YashanDB部署为分支数据库（配置参数ENABLE_BRANCH=TRUE）时，该视图有意义。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| PLAN                  | VARCHAR(64)   | 分支数据库资源计划名称 |
| BRANCH                | VARCHAR(64)   | 当前指令所管理的分支名称 |
| PROFILE               | VARCHAR(64)   | 分支数据库资源模板的名称 |
| SHARES                | NUMBER        | CPU/IO使用的权重份额 |
| UTILIZATION_LIMIT     | NUMBER        | CPU/IOPS的使用最大上限（单位：%） |
| PARALLEL_SERVER_LIMIT | NUMBER        | 可用的最大并行资源百分比（单位：%） |
| MEMORY_MIN            | NUMBER       | 可用内存的下限百分比（单位：%） |
| MEMORY_LIMIT          | NUMBER       | 可用内存的上限百分比（单位：%） |
| COMMENTS              | VARCHAR(2000) | 资源计划指令的注释信息 |
| STATUS                | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MANDATORY             | VARCHAR(3)    | 是否强制存在（无法删除）：是（YES）或不是（NO） |
