本视图显示所有CDB资源计划信息。

仅当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，该视图有意义。


|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| PLAN_ID   | BIGINT        | CDB资源计划的ID |
| PLAN      | VARCHAR(64)   | CDB资源计划的名称 |
| COMMENTS  | VARCHAR(2000) | 资源计划的注释信息 |
| STATUS    | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MANDATORY | VARCHAR(3)    | 是否强制存在（无法删除）：是（YES）或不是（NO） |
