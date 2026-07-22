本视图显示所有CDB资源计划指令信息。

仅当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，该视图有意义。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| PLAN                  | VARCHAR(64)   | CDB资源计划名称 |
| PLUGGABLE_DATABASE    | VARCHAR(64)   | 当前指令所管理的PDB名称 |
| PROFILE               | VARCHAR(64)   | CDB资源模板的名称 | 
| DIRECTIVE_TYPE        | VARCHAR(32)    | 指令类型：<br>\* DEFAULT_DIRECTIVE：CDB默认资源计划指令  <br>\* PDB：专属于指定PDB的CDB资源计划指令 <br>\* PROFILE：CDB资源模板指令 |
| SHARES                | NUMBER        | CPU/IO使用的权重份额 |
| UTILIZATION_LIMIT     | NUMBER        | CPU/IOPS的使用最大上限（单位：%） |
| PARALLEL_SERVER_LIMIT | NUMBER        | 可用的最大并行资源百分比（单位：%） |
| MEMORY_MIN            | NUMBER       | 可用内存的下限百分比（单位：%） |
| MEMORY_LIMIT          | NUMBER       | 可用内存的上限百分比（单位：%） |
| COMMENTS              | VARCHAR(2000) | 资源计划指令的注释信息 |
| STATUS                | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MANDATORY             | VARCHAR(3)    | 是否强制存在（无法删除）：是（YES）或不是（NO） |
