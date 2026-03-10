The XADataSource interface is used to obtain XAConnection objects.

The YashanDB JDBC driver supports the functionality for the XADataSource interface:

|Return Type |Method |
|----------------------|--------------------------------------------------------------------|
| XAConnection         | getXAConnection() throws SQLException;                             |
| XAConnection         | getXAConnection(String user, String password) throws SQLException; |

> **Note**: 
>
> The XADataSource implementation of YashanDB is YasXADataSource. This implementation also inherits from javax.sql.DataSource, so the methods supported in javax.sql.DataSource can be called on YasXADataSource.