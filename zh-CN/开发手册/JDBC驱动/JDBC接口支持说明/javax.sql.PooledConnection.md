当用户通过DataSource建立多个数据库连接时，可将这些连接放在连接池Connection Pool中，此时Connection Pool 可帮助用户实现对物理连接的缓存和重用，能提高应用的性能和可扩展性。

YashanDB对PooledConnection接口已支持功能：

|  返回类型| 方法|
| --- | --- |
| void | addConnectionEventListener​(ConnectionEventListener listener) |
| void | addStatementEventListener​(StatementEventListener listener) |
| void | close() |
| Connection | getConnection() |
| void | removeConnectionEventListener​(ConnectionEventListener listener) |
| void | removeStatementEventListener​(StatementEventListener listener) |
