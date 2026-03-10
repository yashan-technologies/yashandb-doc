本视图显示会话级别buffer访问的统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| SID | INTEGER | 会话ID |
| ACCESS_BLOCKS | BIGINT | 总的最新版本数据块访问数量 |
| CR_BLOCK_GETS | BIGINT | 访问的一致性读数据块数量 |
| DISK_READS | BIGINT | 产生物理读的次数 |
| LOCK_CONFLICTS | BIGINT | block锁冲突的次数 |
| RECYCLE_REQUESTS | BIGINT | 产生buffer淘汰请求的次数 |
| BLOCKS_INSPECTED | BIGINT | buffer淘汰过程中检查的block数量 |
| DIRTY_INSPECTED | BIGINT | buffer淘汰过程中遇到的脏块数量 |
| PINNED_INSPECTED | BIGINT | buffer淘汰过程中遇到的被pin的block数量 |
| HOT_INSPECTED | BIGINT | buffer淘汰过程中遇到的热块数量 |

