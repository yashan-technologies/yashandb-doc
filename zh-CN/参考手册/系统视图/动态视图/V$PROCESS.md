本视图显示系统中所有线程信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| NAME | VARCHAR(32) |线程名    |
| THREAD\_ID | BIGINT | 线程号    |
| THREAD\_ADDR | BIGINT | 线程地址   |
| STACK\_SIZE | INTEGER | 线程栈大小（单位：字节） |
| START\_TIME | TIMESTAMP(6) | 线程启动时间 |
| STATUS | VARCHAR(32) | 线程状态   |
