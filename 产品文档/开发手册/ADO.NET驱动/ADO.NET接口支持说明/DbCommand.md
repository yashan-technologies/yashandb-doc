## DbCommand 类

表示要对数据源执行的SQL语句或存储过程。

- 命名空间: System.Data.Common

### 已支持的方法

| 方法                                 |
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

### 未支持的方法

| 方法                                                         |
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