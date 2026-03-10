## DbDataReader 类

从数据源中读取行的只进流。

- 命名空间: System.Data.Common

### 支持的方法

| 方法                                         |
| -------------------------------------------- |
| Close()                                      |
| Dispose()                                    |
| Dispose(Boolean)                             |
| GetBoolean(Int32)                            |
| GetByte(Int32)                               |
| GetBytes(Int32, Int64, Byte[], Int32, Int32) |
| GetChar(Int32)                               |
| GetChars(Int32, Int64, Char[], Int32, Int32) |
| GetData(Int32)                               |
| GetDataTypeName(Int32)                       |
| GetDateTime(Int32)                           |
| GetDbDataReader(Int32)                       |
| GetDecimal(Int32)                            |
| GetDouble(Int32)                             |
| GetEnumerator()                              |
| GetFieldType(Int32)                          |
| GetFieldValue&lt;T&gt;(Int32)                |
| GetFloat(Int32)                              |
| GetGuid(Int32)                               |
| GetInt16(Int32)                              |
| GetInt32(Int32)                              |
| GetInt64(Int32)                              |
| GetName(Int32)                               |
| GetOrdinal(String)                           |
| GetStream(Int32)                             |
| GetString(Int32)                             |
| GetTextReader(Int32)                         |
| GetValue(Int32)                              |
| GetValues(Object[])                          |
| IsDBNull(Int32)                              |
| NextResult()                                 |
| Read()                                       |

### 未支持的方法

| 方法                                                  |
| ----------------------------------------------------- |
| CloseAsync()                                          |
| DisposeAsync()                                        |
| GetColumnSchemaAsync(CancellationToken)               |
| GetFieldValueAsync&lt;T&gt;(Int32)                    |
| GetFieldValueAsync&lt;T&gt;(Int32, CancellationToken) |
| GetSchemaTable()                                      |
| GetSchemaTableAsync(CancellationToken)                |
| IsDBNullAsync(Int32)                                  |
| IsDBNullAsync(Int32, CancellationToken)               |
| NextResultAsync()                                     |
| NextResultAsync(CancellationToken)                    |
| ReadAsync()                                           |
| ReadAsync(CancellationToken)                          |
