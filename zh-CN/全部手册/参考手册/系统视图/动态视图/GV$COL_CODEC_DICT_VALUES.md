单机部署中，本视图显示所有TAC表的所有字典结构中的值，存在分区时，以分区为单位进行展示。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| BO  | BIGINT | 父表ID |
| OBJID | BIGINT | 本表ID（分区ID） |
| DATAOBJ | BIGINT | 本表的数据对象ID |
| COL_ID  | SMALLINT | 字典编码列在表中的列ID |
| DICT_ID | INTEGER | 本表（分区）中字典项的ID|
| DICT_VALUES |VARCHAR(8000) | 本表（分区）中字典项的值 |
