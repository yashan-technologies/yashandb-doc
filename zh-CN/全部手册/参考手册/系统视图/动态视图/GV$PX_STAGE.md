本视图显示所有节点STAGE信息。

|  字段| 类型| 说明|
|----------------| --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| SQL_ID        | VARCHAR(13) |SQL标识|
| STAGE_ID      | SMALLINT |STAGE标识|
| STATUS        | VARCHAR(64) |STAGE状态|
| NEED_TRIGGER  | TINYINT |STAGE是否需要触发|
| FIRST_STAGE   | SMALLINT |STAGE依赖的第一个STAGE的标识|
| START_TIME    | TIMESTAMP |STAGE启动时间|
