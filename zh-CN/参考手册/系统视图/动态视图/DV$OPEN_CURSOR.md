本视图显示分布式集群中所有节点statement的统计信息及每个statement的内存使用信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| SADDR | RAW(8) | 保留字段 |
| USER_NAME | VARCHAR(64) | 每个statement的用户名 |
| STMT_ID | SMALLINT | 每个statement的ID |
| STATUS | TINYINT | 每个statement的状态<br>\* 0：idle <br>\* 1：prepare <br>\* 2：execute<br>\* 3：fetch<br>\* 4：free |
| PMEM_BLOCK_COUNT | INTEGER | statement占用的私有内存的页面数 |
| PMEM_USED_SIZE | BIGINT | statement当前占用私有内存的空间大小（单位：字节） |
| GMEM_BLOCK_COUNT | INTEGER | statement占用的全局内存的页面数 |
| GMEM_USED_SIZE | BIGINT | statement当前占用全局内存的空间大小（单位：字节） |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| SQL_HASH | BIGINT | SQL语句的哈希值，由SQL文本计算得到 |
