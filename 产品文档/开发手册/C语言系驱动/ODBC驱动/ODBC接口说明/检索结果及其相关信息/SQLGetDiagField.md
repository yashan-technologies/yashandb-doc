## 功能简介

返回诊断数据结构的单个字段的当前值。

## 函数声明

```c
SQLRETURN SQLGetDiagField(  
     SQLSMALLINT     HandleType,  
     SQLHANDLE       Handle,  
     SQLSMALLINT     RecNumber,  
     SQLSMALLINT     DiagIdentifier,  
     SQLPOINTER      DiagInfoPtr,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   StringLengthPtr);
```

## 参数说明

| 参数名                | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| HandleType (IN)       | 描述需要诊断的句柄类型的句柄类型标识符。                     |
| Handle (IN)           | 诊断数据结构的句柄，由HandleType指示的类型。               |
| RecNumber (IN)        | 指示应用程序从中查找信息的状态记录。                         |
| DiagIdentifier (IN)   | 指示要返回其值的诊断字段。                                   |
| DiagInfoPtr (OUT)     | 指向要在其中返回诊断信息的缓冲区的指针。                     |
| BufferLength (IN)     | 如果DiagIdentifier是ODBC定义的诊断，并且 DiagInfoPtr 指向字符串或二进制缓冲区，则此参数应为 \*DiagInfoPtr 的长度。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区将返回总字节数。                   |

DiagIdentifier参数的支持情况：

| 字段属性 | FieldIdentifier                | 支持情况 | 说明                                             |
| -------- | ------------------------------ | -------- | ------------------------------------------------ |
| header   | SQL_DIAG_DYNAMIC_FUNCTION      | 支持     | 返回空串                                         |
| header   | SQL_DIAG_DYNAMIC_FUNCTION_CODE | 支持     | 返回0                                            |
| header   | SQL_DIAG_NUMBER                | 支持     | 返回1（仅支持单错误信息，不支持多层错误码）      |
| header   | SQL_DIAG_RETURNCODE            | 支持     | 返回相应的返回值（SQL_ERROR、SQL_SUCCESS_WITH_INFO等） |
| header   | SQL_DIAG_ROW_COUNT             | 支持     | 同SQLRowCount结果                                |
| record   | SQL_DIAG_CLASS_ORIGIN          | 支持     | 主要用于驱动程序管理器                           |
| record   | SQL_DIAG_CONNECTION_NAME       | 支持     | 返回当前已连接的dsn名称                          |
| record   | SQL_DIAG_MESSAGE_TEXT          | 支持     | 返回错误信息                                     |
| record   | SQL_DIAG_NATIVE                | 支持     | 返回数据库自身的错误码                           |
| record   | SQL_DIAG_SERVER_NAME           | 支持     | 同SQL_DIAG_CONNECTION_NAME                       |
| record   | SQL_DIAG_SQLSTATE              | 支持     | 返回sqlState                                     |
| record   | SQL_DIAG_SUBCLASS_ORIGIN       | 支持     | 主要用于驱动程序管理器                           |
| header   | SQL_DIAG_CURSOR_ROW_COUNT      | 不支持   | 返回SQL_NO_DATA_FOUND                            |
| record   | SQL_DIAG_COLUMN_NUMBER         | 不支持   | 返回SQL_COLUMN_NUMBER_UNKNOWN                    |
| record   | SQL_DIAG_ROW_NUMBER            | 不支持   | 返回SQL_ROW_NUMBER_UNKNOWN                       |


