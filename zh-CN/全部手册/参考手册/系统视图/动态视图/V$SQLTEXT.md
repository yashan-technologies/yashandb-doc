本视图显示所有SQL文本相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ADDRESS | RAW(8) | SQL地址 |
| HASH_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| COMMAND_TYPE | INTEGER | SQL的命令类型<br/>* 1：SQL_QUERY  <br/>* 2：SQL_INSERT  <br/>* 3：SQL_UPDATE  <br/>* 4：SQL_DELETE  <br/>* 5：SQL_MERGE  <br/>* 6：SQL_WITH  <br/>* 7：SQL_ANONYMOUS_BLOCK |
| PIECE | INTEGER | SQL文本的排序编号 |
| SQL_TEXT | VARCHAR(1000) | 将SQL文本按最大1000字符宽度做切片，对应的文本片段序号即为PIECE字段值 |
