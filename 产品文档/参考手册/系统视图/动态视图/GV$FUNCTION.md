本视图显示当前系统提供的所有内置函数信息，具体清单见开发手册[内置函数](../../../开发手册/SQL参考手册/内置函数（yashan模式）/00内置函数（yashan模式）)。

| 字段  | 类型  | 说明                        |
| --- | --- |---------------------------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID  | SMALLINT | 函数的ID                     |
| NAME | VARCHAR(64) | 函数的名称                     |
| ISAGGR | VARCHAR(8) | 是否为聚集函数<br>\*  Y：是<br>\*  N：否 |