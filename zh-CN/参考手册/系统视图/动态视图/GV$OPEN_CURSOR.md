查看每个statement相关信息以及使用的公共堆内存池情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID | SMALLINT | 会话ID |
| SADDR | RAW(8) | 保留字段 |
| USER\_NAME | VARCHAR(64) | 每个statement的用户名 |
| STMT\_ID | SMALLINT | 每个statement的ID |
| STATUS | TINYINT | 每个statement的状态<br>\* 0：idle <br>\* 1：prepare <br>\* 2：execute<br>\* 3：fetch<br>\* 4：free |
| PMEM\_BLOCK\_COUNT | INTEGER | statement占用的私有内存的页面数 |
| PMEM\_USED\_SIZE | BIGINT | statement当前占用私有内存的空间大小（单位：字节） |
| GMEM\_BLOCK\_COUNT | INTEGER | statement占用的全局内存的页面数 |
| GMEM\_USED\_SIZE | BIGINT | statement当前占用全局内存的空间大小（单位：字节） |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| SQL\_HASH | BIGINT | SQL语句的哈希值，由SQL文本计算得到 |
