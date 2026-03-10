本视图显示所有database link在沙箱进程yex_server上使用的内存的相关统计信息。

| 字段 | 类型 | 描述 |
|-------------------|-------------|--|
| EXT_DRIVER_NAME | VARCHAR(64) | 驱动名称 |
| EXT_CONNECTION_COUNT | INTEGER | 驱动上与远程数据库建立连接的数量 |
| EXT_CONNECTION_MEMORY | INTEGER | 驱动上连接占用内存大小 |
| EXT_STATEMENT_MEMORY | INTEGER | 驱动上语句执行资源占用内存大小 |