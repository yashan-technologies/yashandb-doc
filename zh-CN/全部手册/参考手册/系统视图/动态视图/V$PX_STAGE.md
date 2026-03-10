本视图显示当前节点的STAGE信息。

|  字段| 类型| 说明|
|----------------| --- | --- |
| SQL_ID        | VARCHAR(13) |SQL标识|
| STAGE_ID      | SMALLINT |STAGE标识|
| STATUS        | VARCHAR(64) |STAGE状态|
| NEED_TRIGGER  | TINYINT |STAGE是否需要触发|
| FIRST_STAGE   | SMALLINT |STAGE依赖的第一个STAGE的标识|
| START_TIME    | TIMESTAMP(6) |STAGE启动时间|
