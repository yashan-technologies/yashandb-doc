## Preface

In the field of Java development, database operations are the core requirement of most enterprise-level applications and backend services — from user information storage, transaction record retention to massive data statistical analysis, all rely on efficient interaction between Java programs and databases. As the official standard for Java language to access databases, JDBC is the key bridge to break down the barriers between Java programs and various databases, supporting the stable operation of countless Java applications.

## Definition of JDBC

The full name of JDBC is Java DataBase Connectivity. It is not a specific database product nor a directly runnable tool, but a set of standard Application Programming Interface (Java API) specifications formulated by Oracle Corporation (formerly Sun Microsystems).

Its core essence is to provide Java programmers with a unified programming interface, shielding the underlying implementation differences of different databases (such as Oracle, MySQL, YashanDB, PostgreSQL, etc.). This allows developers to operate various relational databases through unified code by following JDBC specifications, without writing different access codes for different databases.

## Functions of JDBC

The core goals of the JDBC specification are "unified interface, shielding differences, and simplifying development". Specifically, it mainly realizes the following 4 core functions, covering the entire process of database operations:

1. Establish connection between Java program and database: Through the interface provided by JDBC, developers can specify database address, user name, password and other information to establish a stable communication link between the program and the database, which is the premise of all database operations.

2. Send SQL statements to the database: After the connection is established, the Java program can send various SQL statements such as query (SELECT), insert (INSERT), update (UPDATE), delete (DELETE) to the database through the JDBC interface to operate data.

3. Process results returned by the database: After the database executes the SQL statement, it will return corresponding results (such as queried data, the number of rows affected by the operation). JDBC will encapsulate these results into objects recognizable by Java programs for developers to obtain and process.

4. Manage database connections and exceptions: JDBC provides interfaces for connection management and transaction control (such as commit and rollback), and defines a unified exception handling mechanism to help developers avoid problems such as connection leakage and operation failure, ensuring program stability.

For Java developers, mastering JDBC is the foundation and core — whether directly using JDBC to write database operation code or using frameworks such as MyBatis, understanding the underlying principles and working mechanism of JDBC can help us better deal with various problems in database development and write more efficient and stable code.

## YashanDB JDBC Introduction

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
- DataSource/PooledConnection/ConnectionPoolDataSource: Data source/pooled connection/connection pool data source.
- Blob/Clob: Operates on large object data.

In addition to the above interfaces, the YashanDB JDBC driver provides a proprietary debugger interface for debugging stored procedures. For specific implementations of the above interfaces, see the section [JDBC Interface Support Overview](./YashanDB JDBC Reference/Status of Support for JDBC Interfaces/00Status of Support for JDBC Interfaces).

Each version of YashanDB's product installation package comes with a JDBC driver jar package. After installation, developers can use standard JDBC and SQL to connect to and manipulate YashanDB. For specific installation steps, see [YashanDB JDBC Driver Installation](./YashanDB JDBC Driver Installation).

For a simple process of connecting and operating YashanDB in Java applications, refer to [JDBC Quick Start](./YashanDB JDBC Quick Start/00YashanDB JDBC Quick Start).

The YashanDB JDBC driver also includes the following main functionalities:

- Data type conversion between Java and YashanDB; see the description in the section [JDBC Data Type Mapping Relationships](./YashanDB JDBC Reference/Data Type Mapping Relationships in JDBC Driver/00Data Type Mapping Relationships in JDBC Driver).
- Automatically identifies and connects to the primary database in a highly available primary/standby deployment.
- Automatically identifies and connects to the node with the least load in load-balanced YAC/Distributed Cluster and ISC Distributed Cluster Deployment.
- Timeout mechanisms, including connection timeout, socket timeout, SQL execution timeout, etc.
- Transparent application failover functionality, allowing the client to automatically reconnect to other configured nodes after a failure occurs in the current connection node; see the description in [JDBC Transparent Application Failover](./YashanDB JDBC User Guide/Transparent Application Failover).

> **Note**:
>
> If there are any JDBC-related compatibility issues, please contact our technical support for assistance.
