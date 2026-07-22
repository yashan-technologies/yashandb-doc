本视图显示所有sql文本相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ADDRESS | RAW(8) | SQL地址 |
| HASH\_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| COMMAND\_TYPE | INTEGER | SQL的命令类型<br/>* 1：SQL\_QUERY  <br/>* 2：SQL\_INSERT  <br/>* 3：SQL\_UPDATE  <br/>* 4：SQL\_DELETE  <br/>* 5：SQL\_MERGE  <br/>* 6：SQL\_WITH  <br/>* 7：SQL\_ANONYMOUS\_BLOCK |
| PIECE | INTEGER | SQL文本的排序编号 |
| SQL\_TEXT | VARCHAR(1000) | 将SQL文本按1000字节做切片的有效文本片段，文本片段序号见PIECE字段 |
