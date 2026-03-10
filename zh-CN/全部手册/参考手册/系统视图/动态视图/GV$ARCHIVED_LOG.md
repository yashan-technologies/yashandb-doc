本视图显示归档文件统计信息。

|  字段| 类型| 说明|
|------------------|--------------| --- |
| GROUP_ID         | NUMBER       | 组ID |
| GROUP_NODE_ID    | NUMBER       | 组内节点ID |
| INST_ID         | NUMBER       | 实例ID|
| NAME             | VARCHAR(255) | 归档文件路径 |
| SEQUENCE#        | INTEGER      | 归档文件序列号 |
| THREAD#          | TINYINT      | 实例编号 |
| RESETLOGS_ID    | INTEGER      | 归档文件reset id |
| FIRST_CHANGE#   | BIGINT       | 归档文件内，第一条日志的SCN |
| FIRST_TIME      | TIMESTAMP    | 归档文件内，第一条日志的SCN对应的时间 |
| NEXT_CHANGE#    | BIGINT       | 归档文件内，最后一条日志的SCN |
| NEXT_TIME       | TIMESTAMP    | 归档文件内，最后一条日志的SCN对应的时间 |
| BLOCKS           | BIGINT       | 归档文件的页面数量 |
| BLOCK_SIZE      | INTEGER      | 归档文件的页面大小（单位：字节） |
| COMPLETION_TIME | DATE         | 归档文件的生成时间 |
| COMPRESSED       | VARCHAR(8)   | 是否是压缩文件 |
| FAL              | VARCHAR(8)   | 归档日志是否由FAL线程生成 |
| REGISTRAR        | VARCHAR(8)   | 归档的注册方式<br>\* MANUAL：手动注册<br>\* ARCH：归档线程注册 |
| STATUS           | VARCHAR(3)   | 归档的状态 <br>\* A：表示Available |
| ARCHIVED         | VARCHAR(3)   | 在线redo是否被归档<br>\* YES：已被归档<br>\* NO：未归档 |
| DELETED          | VARCHAR(3)   | 归档文件是否被删除<br>\* YES：文件已被删除<br>\* NO：文件未被删除 |
| BACKUP_COUNT     | TINYINT      | 归档文件备份次数，初始值为0，以该归档文件的备份次数累加，最大值为15 |
