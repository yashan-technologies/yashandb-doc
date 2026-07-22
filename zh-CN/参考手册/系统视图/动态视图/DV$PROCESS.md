本视图显示存算一体分布式集群中所有节点当前线程信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| NAME | VARCHAR(32) |线程名    |
| THREAD\_ID | BIGINT | 线程号    |
| THREAD\_ADDR | BIGINT | 线程地址   |
| STACK\_SIZE | INTEGER | 线程栈大小（单位：字节） |
| START\_TIME | TIMESTAMP(6) | 线程启动时间 |
| STATUS | VARCHAR(32) | 线程状态   |
