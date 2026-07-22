本视图显示存算一体分布式集群中的主备链路配置信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| DEST\_ID | TINYINT | 备库ID，与ARCHIVE\_DEST\_x参数相对应 |
| DEST\_NAME | VARCHAR(16) | 参数名称 |
| SERVICE | VARCHAR(256) | 远程服务地址 |
| NET\_TIMEOUT | BIGINT | 最大可用模式下，事务提交等待备库响应的超时时间 |
| AFFIRM | VARCHAR(8) | 是否等待备库日志刷盘后，再返回ACK |
| VALID\_NOW | VARCHAR(8) | 链路参数是否生效： <br/>* YES ：此链路的数据库角色对当前数据库有效  <br/>* NO ：此链路指定的数据库角色不是当前运行数据库的角色，该链路未生效 |
| VALID\_ROLE | VARCHAR(16) | 对链路生效的数据库角色 |
| DB\_UNIQUE\_NAME | VARCHAR(31) | 唯一的数据库名称 |
| NODE\_ID | VARCHAR(64) | 节点编号，格式service\_id-group\_id-node\_id |
| DISABLE\_ELECTION   | VARCHAR(8) | 标记该链路指向的节点是否参与主备集群的自动选主或者投票。默认为FALSE，若指定为TRUE，即标记该链路指向节点只同步数据库，不参与自动选主的投票和选举  |
