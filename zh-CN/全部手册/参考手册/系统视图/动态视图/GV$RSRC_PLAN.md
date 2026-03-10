本视图显示容器数据库环境中所有使用中的资源计划信息。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | 组ID               |
| GROUP_NODE_ID | NUMBER       | 组内节点ID                |
| INST_ID       | NUMBER       | 实例ID                   |
| ID                         | BIGINT      | 资源计划ID |
| NAME                       | VARCHAR(68) | 资源计划名称 |
| IS_TOP_PLAN                | VARCHAR(16) | 是否一级计划，目前仅支持一级计划，值固定为`TRUE` |
| CPU_MANAGED                | VARCHAR(16) | CPU资源是否被管控，目前值固定为`ON` |
| CPU_SCOPE                  | VARCHAR(16) | CPU管理策略，目前值固定为`SERVER_WIDE`，表示实例内部管理 |
| INSTANCE_CAGING            | VARCHAR(16) | 实例是否受CPU_COUNT严格限制，目前值固定未`OFF` |
| PARALLEL_SERVERS_ACTIVE    | INTEGER     | 实例活跃并行线程数量 |
| PARALLEL_SERVERS_TOTAL     | INTEGER     | 实例全部并行线程数量 |
| PARALLEL_EXECUTION_MANAGED | VARCHAR(32) | 仅用于兼容，目前值固定为`NULL` |
| CON_ID                     | BIGINT      | 容器ID |
| DIRECTIVE_TYPE             | VARCHAR(32) | 指令类型：<br>\* DEFAULT_DIRECTIVE：使用默认值  <br>\* PDB：指定PDB计划指令 <br>\* PROFILE：使用性能配置 |
| SHARES                     | INTEGER     | 容器可用CPU/IO使用的权重份额 |
| UTILIZATION_LIMIT          | INTEGER     | 容器可用CPU/IO最大使用上限（单位：%） |
| PARALLEL_SERVER_LIMIT      | INTEGER     | 容器可用并行资源最大使用上限（单位：%） |
| MEMORY_MIN                 | INTEGER     | 容器可用内存的最小比例（单位：%） |
| MEMORY_LIMIT               | INTEGER     | 容器可用内存的最大比例（单位：%） |
| PROFILE                    | VARCHAR(68) | 容器使用的性能配置 |
| CPU_COUNT                  | INTEGER     | 仅用于兼容，目前值固定为`NULL` |
| CPU_MIN_COUNT              | INTEGER     | 仅用于兼容，目前值固定为`NULL` |
