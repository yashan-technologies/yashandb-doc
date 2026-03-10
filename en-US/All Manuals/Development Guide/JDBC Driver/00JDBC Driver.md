JDBC (Java DataBase Connectivity) is a standard application programming interface (Java API) for accessing databases in the Java language. Java programmers manipulate databases using a series of abstract classes defined in the SQL package.

The YashanDB JDBC driver is YashanDB's implementation of the Java API, which includes the following interfaces from the SQL package:

- Connection: The connection from the JDBC client to the database.
- DatabaseMetaData: Obtains comprehensive information about the database.
- Statement: Sends SQL commands to the database and retrieves result sets from the database.
- PreparedStatement: Pre-compiles SQL statements.
- ParameterMetaData: Obtains information about the types and attributes of the parameters bound in PreparedStatement.
- CallableStatement: Calls PL objects.
- ResultSet: The result set after executing the SQL query statement.
- ResultSetMetadata: Obtains information about the types and attributes of the columns in the result set.
- Driver: The JDBC driver interface.
- DataSource/PooledConnection/ConnectionPoolDataSource: Data source / connection to data source / connection pool.
- Blob/Clob: The operation interfaces for LOBs.

In addition to the above interfaces, the YashanDB JDBC driver provides a proprietary debugger interface for debugging stored procedures. For specific implementations of the above interfaces, see the section [JDBC Interface Support Overview](./JDBC接口支持说明/00JDBC接口支持说明).

Each version of YashanDB's product installation package comes with a JDBC driver jar package. After installation, developers can use standard JDBC and SQL to connect to and manipulate YashanDB. For specific installation steps, see [YashanDB JDBC Driver Installation](./安装JDBC驱动).

For a simple process of connecting and operating YashanDB in Java applications, refer to [YashanDB JDBC Driver Usage Introduction](./JDBC驱动使用介绍).

The YashanDB JDBC driver also includes the following main functionalities:

- Data type conversion between Java and YashanDB; see the description in the section [JDBC Data Type Mapping Relationships](./JDBC数据类型映射关系/00JDBC数据类型映射关系).
- Automatically identifies and connects to the primary database in a highly available primary/standby deployment.
- Automatically identifies and connects to the node with the least load in load-balanced YACs and ISC Distributed Cluster Deployment.
- Timeout mechanisms, including connection timeout, socket timeout, SQL execution timeout, etc.
- Transparent application failover functionality, allowing the client to automatically reconnect to other configured nodes after a failure occurs in the current connection node; see the description in [JDBC Transparent Application Failover](./JDBC高级功能说明/透明应用程序故障转移).

> **Note**:
> 
> If there are any JDBC-related compatibility issues, please contact our technical support for assistance.