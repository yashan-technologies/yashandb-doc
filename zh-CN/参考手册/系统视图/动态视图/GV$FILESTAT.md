本视图显示已完成的物理读写次数、以文件级别完成的单块和多块I/O总数。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID      | NUMBER | 组ID |
| GROUP\_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID       | NUMBER  | 实例ID |
| FILE#          | INTEGER | 数据文件编号 |
| PHYRDS         | BIGINT  | 完成的物理读取次数 |
| PHYWRTS        | BIGINT  | DBWR 需要写入的次数 |
| PHYBLKRD       | BIGINT  | 读取的物理块数 |
| PHYBLKWRT      | BIGINT  | PHYWRTS 写入的块数量 |
| SINGLEBLKRDS   | BIGINT  | 单块读取次数 |
| READTIM        | BIGINT  | 执行读取所花费的时间（以百分之一秒为单位） |
| WRITETIM       | BIGINT  | 执行写入所花费的时间（以百分之一秒为单位） |
| SINGLEBLKRDTIM | BIGINT  | 累计单块读取时间（以百分之一秒为单位） |
| AVGIOTIM       | BIGINT  |  I/O 所花费的平均时间（以百分之一秒为单位）|
| LSTIOTIM       | BIGINT  | 执行最后一次 I/O 所花费的时间（以百分之一秒为单位） |
| MINIOTIM       | BIGINT  | 单个 I/O 所花费的最短时间（以百分之一秒为单位） |
| MAXIORTM       | BIGINT  | 执行单次读取所花费的最长时间（以百分之一秒为单位） |
| MAXIOWTM       | BIGINT  | 执行单次写入所花费的最长时间（以百分之一秒为单位） |
