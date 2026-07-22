ADO.NET is the core data access technology for .NET languages. This technology defines a set of classes and interfaces for public data access services, providing developers with consistent access to relational data, XML, and application data, making it an indispensable part of the .NET Framework.

The YashanDB ADO.NET driver uses the Yashandb.Data.YashandbClient namespace, implementing most of the classes and interfaces of ADO.NET, allowing .NET applications to access and manipulate YashanDB database data through ADO.NET.

The YashanDB ADO.NET driver only supports connecting to YashanDB deployed in yashan mode.

The YashanDB ADO.NET driver is YashanDB's implementation of the standard ADO.NET, including the following classes. For the specific implementation, please refer to [ADO.NET Interface Support Status](./Status of Support for ADO.NET Interfaces/00Status of Support for ADO.NET Interfaces):

- DbConnection: A connection from the ADO.NET client to the database.
- DbTransaction: Defines the core behavior of database transactions and provides a base class for database-specific transactions.
- DbCommand: Sends SQL commands to the database and retrieves result sets from the database.
- DbParameter: SQL binding parameter method class.
- DbDataReader: Result set retrieval method class.
- DbParameterCollection: A base class for parameter collections related to DbCommand.

The YashanDB ADO.NET driver allows multiple threads to share the same Connection object simultaneously. It adopts a serialized execution mechanism (i.e., only one thread is permitted to perform database operations at a time) to avoid race conditions and data competition issues, thus ensuring data consistency and operation atomicity. To prevent resource leakage, the following are recommended:

- After the business logic is executed, explicitly call the Dispose() method of objects such as Command and DataReader (or use it in conjunction with the using statement) to release the manually requested system resources.

- After disconnecting, explicitly call the Connection.Dispose() method to release the connection resources.
  
>**Caution**:
>
> There is no strict sequential constraint between calling Command.Dispose() and Connection.Dispose(). However, after calling the Connection.Dispose() method, Command operations (such as executing SQL statements and stored procedures) can no longer be performed through this connection object. Please arrange the timing and sequence of resource release reasonably according to the business logic.
>
> The YashanDB ADO.NET driver has implemented a complete protection mechanism for scenarios of repeated resource release to ensure that multiple calls to Dispose() will not lead to undefined behavior.