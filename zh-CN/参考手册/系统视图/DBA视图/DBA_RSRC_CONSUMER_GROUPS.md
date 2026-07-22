本视图显示数据库中所有资源使用组的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| CONSUMER\_GROUP\_ID | BIGINT        | 资源使用组 ID |
| CONSUMER\_GROUP     | VARCHAR(64)   | 资源使用组的名称 |
| CPU\_METHOD         | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MGMT\_METHOD        | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| INTERNAL\_USE       | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| COMMENTS            | VARCHAR(2000) | 资源使用组的注释信息 |
| CATEGORY            | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| STATUS              | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MANDATORY           | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
