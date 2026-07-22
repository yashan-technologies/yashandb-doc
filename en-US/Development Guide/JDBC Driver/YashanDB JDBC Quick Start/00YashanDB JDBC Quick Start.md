JDBC (Java Database Connectivity) is the standard API for Java to operate relational databases. Released by Sun in 1997, it has become part of the Java standard library. JDBC provides a unified interface specification, enabling Java applications to connect and operate various databases (such as MySQL, Oracle, PostgreSQL, and YashanDB) through the same approach.

Before JDBC, Java programmers had to write specific code for each database, which caused serious portability issues. JDBC completely solved this problem — developers only need to write code using the JDBC standard API without worrying about the specific implementation details of the underlying database. When switching databases, you only need to replace the corresponding JDBC driver, greatly improving code portability and development efficiency.

JDBC works based on a layered architecture. The application sends database operation requests through the JDBC API. The JDBC DriverManager selects the appropriate driver based on the database URL, and the driver then converts the request into a protocol recognized by the database for execution. The entire process is transparent to developers, who only need to focus on implementing business logic.

The JDBC API contains several core interfaces and classes that work together to perform database operations:

- **DriverManager**: The driver manager, responsible for loading JDBC drivers and establishing database connections.

- **Connection**: Represents a connection session to the database, used to create Statement objects.

- **Statement**: Used to execute static SQL statements and return results.

- **PreparedStatement**: Pre-compiled SQL statements, supporting parameterized queries and preventing SQL injection.

- **ResultSet**: Represents the result set returned by SQL queries, providing methods to traverse and read data.

A typical JDBC usage process includes the following steps:

1. Load the JDBC driver.

2. Establish a database connection through DriverManager.

3. Create Statement or PreparedStatement objects.

4. Execute SQL statements and process results.

5. Close ResultSet, Statement, and Connection resources.

This introductory tutorial will help you quickly master the core usage of JDBC, covering the entire workflow of database connection, resource closure, and CRUD (Create, Read, Update, Delete) operations — the code is ready to run out of the box.