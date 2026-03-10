本视图显示当前节点的并行执行资源管理信息。

| 字段             | 类型  | 说明  |
|----------------| --- | --- |
| CONSUMER_GROUP        | VARCHAR(64) | 资源组 |
| PX_RES_TYPE           | VARCHAR(64) | 资源类型 |
| PX_RES_LIMIT         | INTEGER | 资源使用上限 |
| MAX_PX_RES_USAGE    | INTEGER |资源最大使用量 |
| CURR_PX_RES_USAGE  | INTEGER | 资源当前使用量 |