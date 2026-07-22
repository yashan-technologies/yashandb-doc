本视图显示会话级别buffer访问的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| SID | INTEGER | 会话ID |
| ACCESS\_BLOCKS | BIGINT | 总的最新版本数据块访问数量 |
| CR\_BLOCK\_GETS | BIGINT | 访问的一致性读数据块数量 |
| DISK\_READS | BIGINT | 产生物理读的次数 |
| LOCK\_CONFLICTS | BIGINT | block锁冲突的次数 |
| RECYCLE\_REQUESTS | BIGINT | 产生buffer淘汰请求的次数 |
| BLOCKS\_INSPECTED | BIGINT | buffer淘汰过程中检查的block数量 |
| DIRTY\_INSPECTED | BIGINT | buffer淘汰过程中遇到的脏块数量 |
| PINNED\_INSPECTED | BIGINT | buffer淘汰过程中遇到的被pin的block数量 |
| HOT\_INSPECTED | BIGINT | buffer淘汰过程中遇到的热块数量 |
