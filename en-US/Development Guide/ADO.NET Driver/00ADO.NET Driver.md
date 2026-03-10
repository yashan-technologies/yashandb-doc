## ADO.NET

ADO.NET is the core data access technology for .NET languages. This technology defines a set of classes and interfaces for public data access services, providing developers with consistent access to relational data, XML, and application data, making it an indispensable part of the .NET Framework.

The driver supports connections only when YashanDB is deployed in yashan mode.

The YashanDB ADO.NET driver uses the Yashandb.Data.YashandbClient namespace, implementing most of the classes and interfaces of ADO.NET, allowing .NET applications to access and manipulate YashanDB database data through ADO.NET.

The YashanDB ADO.NET driver is YashanDB's implementation of standard ADO.NET, including the following classes:

- DbConnection: A connection from the ADO.NET client to the database.
- DbTransaction: Defines the core behavior of database transactions and provides a base class for database-specific transactions.
- DbCommand: Sends SQL commands to the database and retrieves result sets from the database.
- DbParameter: SQL binding parameter method class.
- DbDataReader: Result set retrieval method class.
- DbParameterCollection: A base class for parameter collections related to DbCommand.

For specific implementations of the above content, refer to the section on [ADO.NET Interface Support Status](Status of Support for ADO.NET Interfaces/00Status of Support for ADO.NET Interfaces).

> **Caution**:
>
> The YashanDB ADO.NET driver does not support connections to YashanDB installed in mysql mode.