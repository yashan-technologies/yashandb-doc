## DbConnection

DbConnection defines the core behaviors of database connections and provides a base class for database-specific connections.

- Namespace: System.Data.Common

### Supported Methods

|Method |
| ----------------------------------- |
| BeginDbTransaction(IsolationLevel)  |
| BeginTransaction()                  |
| BeginTransaction(IsolationLevel)    |
| Close()                             |
| CreateCommand()                     |
| CreateDbCommand()                   |
| Dispose()                           |
| Dispose(Boolean)                    |
| OnStateChange(StateChangeEventArgs) |
| Open()                              |

### Unsupported Methods

|Method |
| ---------------------------------------------------------- |
| BeginDbTransactionAsync(IsolationLevel, CancellationToken) |
| BeginTransactionAsync(CancellationToken)                   |
| BeginTransactionAsync(IsolationLevel, CancellationToken)   |
| ChangeDatabase(String)                                     |
| ChangeDatabaseAsync(String, CancellationToken)             |
| CloseAsync()                                               |
| DisposeAsync()                                             |
| EnlistTransaction(Transaction)                             |
| GetSchema()                                                |
| GetSchema(String)                                          |
| GetSchema(String, String[])                                |
| GetSchemaAsync(CancellationToken)                          |
| GetSchemaAsync(String, CancellationToken)                  |
| GetSchemaAsync(String, String[], CancellationToken)        |
| OpenAsync()                                                |
| OpenAsync(CancellationToken)                               |
