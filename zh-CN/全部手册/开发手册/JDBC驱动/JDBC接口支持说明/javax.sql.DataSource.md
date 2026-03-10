DataSource接口支持用户通过一次配置DataSource的属性后，在不同地方均可建立数据库连接Connection。

YashanDB JDBC驱动对DataSource接口已支持功能：

|  返回类型| 方法|
| --- | --- |
| Connection | getConnection() |
| Connection | getConnection(String username, String password) |
| void | setUser(String userID) |
| String | getUser() |
| void | setPassword(String password) |
| void | setPortNumber(int port) |
| String | getPortNumber() |
| void | setServerName(String sn) |
| String | getServerName() |
| void | setURL(String url) |
| String | getURL() |
| void   | setDatabaseName(String name)  |
| String  | getDatabaseName()  |
| Reference | getReference() |
| void | setLoginTimeout(int seconds) |
| int | getLoginTimeout() |
| void | setSslRootCer(String sslRootCer) |
| String | getSslRootCer() |
| void | setDescription(String value) |
| String | getDescription() |

未支持功能：

|  返回类型| 方法|
| --- | --- |
| void | setLogWriter(PrintWriter out) |
| PrintWriter | getLogWriter()  |
| void | getParentLogger() |
