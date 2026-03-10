## DbConnection

DbConnecion定义数据库连接的核心行为，并为数据库专用连接提供基类。

- 命名空间: System.Data.Common

### 已支持的方法

| 方法                                |
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

### 未支持的方法

| 方法                                                       |
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