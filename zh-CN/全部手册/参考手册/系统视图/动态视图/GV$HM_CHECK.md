故障诊断视图，显示当前所有的健康巡检项目信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID | INTEGER | 健康检查ID |
| NAME | VARCHAR(64) | 健康检查名称 |
| OFFLINE_CAPABLE | VARCHAR(3) | 能够在数据库尚未打开时运行，YES或NO |
| DESCRIPTION | VARCHAR(1024) | 检查功能的说明 |
