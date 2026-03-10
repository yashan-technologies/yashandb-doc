## DbDataReader Class

A forward-only stream for reading rows from a data source.

- Namespace: System.Data.Common

### Supported Methods

|Method |
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

### Unsupported Methods

|Method |
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
