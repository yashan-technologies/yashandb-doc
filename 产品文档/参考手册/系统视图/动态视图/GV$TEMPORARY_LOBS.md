本视图显示所有临时LOB的相关统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| SID | SMALLINT | 会话ID |
| CACHE_LOBS | BIGINT | 保留字段，仅语法兼容，值为0 |
| NOCACHE_LOBS | BIGINT | 通过调用DBMS_LOB.CREATETEMPORARY创建的临时LOB数量 |
| ABSTRACT_LOBS | BIGINT | 隐式创建的临时LOB数量，暂不统计，值为0 |