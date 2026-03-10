The DataSource interface allows users to establish database connections (Connection) in different places after configuring the properties of the DataSource once.

The YashanDB JDBC driver supports the following functionalities for the DataSource interface:

|Return Type |Method |
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
| ShardingKeyBuilder | createShardingKeyBuilder() |
| ConnectionBuilder | createConnectionBuilder() |

The following functionalities are not supported:

|Return Type |Method |
| --- | --- |
| void | setLogWriter(PrintWriter out) |
| PrintWriter | getLogWriter()  |
| void | getParentLogger() |
