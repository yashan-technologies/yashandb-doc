本视图显示当前正在执行的SQL语句汇总信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ADDRESS | RAW(8) | SQL地址 |
| HASH_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| COMMAND_TYPE | INTEGER | SQL的命令类型<br/>* 1：SQL_QUERY  <br/>* 2：SQL_INSERT  <br/>* 3：SQL_UPDATE  <br/>* 4：SQL_DELETE  <br/>* 5：SQL_MERGE  <br/>* 6：SQL_WITH  <br/>* 7：SQL_ANONYMOUS_BLOCK |
| PIECE | INTEGER | SQL文本的排序编号 |
| SQL_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |