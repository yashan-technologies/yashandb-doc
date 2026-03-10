XADataSource接口用于获取XAConnection对象。

YashanDB JDBC驱动对XADataSource接口已支持功能：

|  返回类型| 方法|
|----------------------|--------------------------------------------------------------------|
| XAConnection         | getXAConnection() throws SQLException;                             |
| XAConnection         | getXAConnection(String user, String password) throws SQLException; |

> **Note**: 
>
> YashanDB的XADataSource实现为YasXADataSource，该实现同时继承了javax.sql.DataSource，所以javax.sql.DataSource中支持的方法在YasXADataSource上均可调用。
