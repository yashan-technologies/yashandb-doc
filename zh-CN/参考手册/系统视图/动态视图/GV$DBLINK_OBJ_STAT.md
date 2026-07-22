本视图显示所有database link在沙箱进程yex_server上创建的对象的相关统计信息。

|  字段| 类型| 描述|
|-------------------|-------------|--|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| EXT\_OBJ\_ID | INTEGER | 对象ID |
| EXT\_OBJ\_NAME | VARCHAR(64) | 对象名称 |
| EXT\_OBJ\_USERID | INTEGER | 对象USERID |
| EXT\_OBJ\_VALID | BOOLEAN | 对象是否有效，TRUE表示有效，FALSE表示无效 |
| EXT\_OBJ\_REF | INTEGER | 对象被引用的计数值 |
