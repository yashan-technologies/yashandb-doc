本视图显示数据库中用户和资源使用组之间的映射关系。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| ATTRIBUTE       | VARCHAR(64) | 属性，当前仅支持`USER` |
| VALUE           | VARCHAR(64) | 属性值，当属性为`USER`时，记录的是用户名 |
| CONSUMER_GROUP | VARCHAR(64) | 用户映射的资源使用组的名称 |
| STATUS          | VARCHAR(1)  | 仅用于兼容，目前值固定为`NULL` |