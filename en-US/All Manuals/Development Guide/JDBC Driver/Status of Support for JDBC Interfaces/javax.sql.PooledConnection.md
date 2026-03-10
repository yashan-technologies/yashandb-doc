When users establish multiple database connections through DataSource, these connections can be placed in a Connection Pool. At this time, the Connection Pool can help users buffer and reuse physical connections, enhancing application performance and scalability.

YashanDB supports the functionality of the PooledConnection interface:

|Return Type |Method |
| --- | --- |
| void | addConnectionEventListener​(ConnectionEventListener listener) |
| void | addStatementEventListener​(StatementEventListener listener) |
| void | close() |
| Connection | getConnection() |
| void | removeConnectionEventListener​(ConnectionEventListener listener) |
| void | removeStatementEventListener​(StatementEventListener listener) |
