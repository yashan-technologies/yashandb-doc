## DbCommand Class

Represents a SQL statement or stored procedure to execute against a data source.

- Namespace: System.Data.Common

### Supported Methods

|Method |
| ------------------------------------ |
| Cancel()                             |
| CreateDbParameter()                  |
| CreateParameter()                    |
| Dispose()                            |
| Dispose(Boolean)                     |
| ExecuteDbDataReader(CommandBehavior) |
| ExecuteNonQuery()                    |
| ExecuteReader()                      |
| ExecuteReader(CommandBehavior)       |
| ExecuteScalar()                      |
| Prepare()                            |

### Unsupported Methods

|Method |
| ------------------------------------------------------------ |
| DisposeAsync()                                               |
| ExecuteDbDataReaderAsync(CommandBehavior, CancellationToken) |
| ExecuteNonQueryAsync()                                       |
| ExecuteNonQueryAsync(CancellationToken)                      |
| ExecuteReaderAsync()                                         |
| ExecuteReaderAsync(CancellationToken)                        |
| ExecuteReaderAsync(CommandBehavior)                          |
| ExecuteReaderAsync(CommandBehavior, CancellationToken)       |
| ExecuteScalarAsync()                                         |
| ExecuteScalarAsync(CancellationToken)                        |
| PrepareAsync(CancellationToken)                              |
