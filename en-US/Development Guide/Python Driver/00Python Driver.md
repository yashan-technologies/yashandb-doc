In Python development, database operations are the core requirement of most enterprise-level applications and backend services — from user information storage, transaction record retention to massive data statistical analysis, all rely on efficient interaction between Python programs and databases. As the official standard for Python to access YashanDB databases, the YashanDB Python driver is the key bridge to break down the barriers between Python programs and YashanDB databases.

The YashanDB Python driver (python-yaspy) is an official Python database driver provided by YashanDB. It follows the Python DB API v2.0 specification and is a Python extension module that supports the [Python DB API protocol](https://peps.python.org/pep-0249/). This module enables general Python applications to directly connect to YashanDB databases and provides Python applications with the ability to access YashanDB databases.

Its core essence is to provide Python programmers with a unified programming interface, shielding the underlying implementation differences of different databases. This allows developers to operate YashanDB databases through unified code by following the Python DB API specifications.

This driver supports connections only when YashanDB is deployed in yashan mode.

YashanDB provides two Python driver packages: yaspy and yasdb. Either package can be installed to connect to the YashanDB database.

YashanDB v23.4.4 has introduced performance improvements for the yaspy module (significant performance advantages over yasdb under the same test environment). Additionally, the priority for future feature development will be: yaspy > yasdb. The yaspy module is recommended.

This chapter uses yaspy as an example, and the yasdb module can be referenced for similar operations.

The YashanDB Python Driver supports the following features:

- **Database connection management**: supports standalone connections and connection pool mode
- **SQL execution**: supports executing DDL, DML and other SQL statements
- **Transaction management**: supports transaction commit and rollback
- **Parameterized queries**: supports named parameters (`:name`) and positional parameters (`?`)
- **Connection pool**: supports SessionPool to improve performance in high-concurrency scenario

## Technical Specifications

|Item |Description |
| --------------- | -------------------- |
| Supported Language | Python 3.6+ |
| Supported Platforms | Windows、Linux |
| API Specification | Python DB API v2.0 |
| Supported Connection Methods | Standalone connection, connection pool |
| Parameter Style | named, positional |

## Problems Solved by Python Driver

1. **Simplify database operations**: Through unified API interfaces, developers can use the same code style to execute SQL statements and retrieve query results without worrying about the underlying database communication details.

2. **Improve development efficiency**: Built-in advanced features such as connection pooling, parameterized queries, and transaction management reduce repetitive code writing.

3. **Ensure program stability**: Provides complete exception handling mechanism and connection management to help developers avoid problems such as connection leakage and operation failure.
