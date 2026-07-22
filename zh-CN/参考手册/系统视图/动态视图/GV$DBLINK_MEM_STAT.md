本视图显示所有database link在沙箱进程yex_server上使用的内存的相关统计信息。

|  字段| 类型| 描述|
|-------------------|-------------|--|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| EXT\_DRIVER\_NAME | VARCHAR(64) | 驱动名称 |
| EXT\_CONNECTION\_COUNT | INTEGER | 驱动上与远程数据库建立连接的数量 |
| EXT\_CONNECTION\_MEMORY | INTEGER | 驱动上连接占用内存大小 |
| EXT\_STATEMENT\_MEMORY | INTEGER | 驱动上语句执行资源占用内存大小 |
