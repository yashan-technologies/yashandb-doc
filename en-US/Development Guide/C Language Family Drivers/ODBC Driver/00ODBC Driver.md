ODBC (Open Database Connectivity) provides a standard API (Application Programming Interface) method to access databases, utilizing SQL to accomplish most of its tasks. The standard ODBC interface defines the following:

- ODBC function call library, which allows applications to connect to a data source, execute SQL commands, and retrieve results.
- SQL syntax based on the SQL-99 specification.
- A set of standard error codes.
- A standard method to connect and log in to data sources.
- A standard description of data types.

The driver is only supported for connection when YashanDB is deployed in yashan mode.

The YashanDB ODBC driver is YashanDB's implementation of the ODBC API. Official documentation for the ODBC API can be referenced at [ODBC API Reference - ODBC API Reference | Microsoft Learn](https://learn.microsoft.com/zh-cn/sql/odbc/reference/syntax/odbc-api-reference?view=sql-server-ver16).

Supported OS + CPU Architecture Combinations for ODBC Driver:

- Windows 7~11  + x64

- Windows 7~10  + x86

- Linux + ARM64

- Linux + LoongArch64

- Linux + x86_64
