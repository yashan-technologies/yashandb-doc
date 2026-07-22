## Functionality Introduction

Returns the current value of a single field in the diagnostic data structure.

## Function Declaration

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

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| HandleType (IN)       | A handle type identifier that describes the type of handle to be diagnosed. |
| Handle (IN)           | A handle to the diagnostic data structure indicated by HandleType. |
| RecNumber (IN)        | Indicates the status record from which the application seeks information. |
| DiagIdentifier (IN)   | Indicates the diagnostic field whose value is to be returned. |
| DiagInfoPtr (OUT)     | A pointer to the cache where the diagnostic information will be returned. |
| BufferLength (IN)     | If DiagIdentifier is an ODBC-defined diagnostic, this parameter should be the length of *DiagInfoPtr if it points to a string or binary cache. |
| StringLengthPtr (OUT) | A pointer to a cache where the total byte count will be returned. |

Support for the DiagIdentifier parameter:

|Field Attribute |FieldIdentifier |Support Status |Description |
| -------- | ------------------------------ | -------- | ------------------------------------------------ |
| header          | SQL_DIAG_DYNAMIC_FUNCTION      | Supported      | Returns an empty string                        |
| header          | SQL_DIAG_DYNAMIC_FUNCTION_CODE | Supported      | Returns 0                                      |
| header          | SQL_DIAG_NUMBER                | Supported      | Returns 1 (supports only single error message; does not support multilayer error codes) |
| header          | SQL_DIAG_RETURNCODE            | Supported      | Returns the corresponding return value (SQL_ERROR, SQL_SUCCESS_WITH_INFO, etc.) |
| header          | SQL_DIAG_ROW_COUNT             | Supported      | Same as SQLRowCount result                     |
| record          | SQL_DIAG_CLASS_ORIGIN          | Supported      | Primarily used for the driver manager         |
| record          | SQL_DIAG_CONNECTION_NAME       | Supported      | Returns the name of the currently connected DSN |
| record          | SQL_DIAG_MESSAGE_TEXT          | Supported      | Returns error information                       |
| record          | SQL_DIAG_NATIVE                | Supported      | Returns the database's own error code         |
| record          | SQL_DIAG_SERVER_NAME           | Supported      | Same as SQL_DIAG_CONNECTION_NAME               |
| record          | SQL_DIAG_SQLSTATE              | Supported      | Returns SQL state                              |
| record          | SQL_DIAG_SUBCLASS_ORIGIN       | Supported      | Primarily used for the driver manager         |
| header          | SQL_DIAG_CURSOR_ROW_COUNT      | Not Supported   | Returns SQL_NO_DATA_FOUND                     |
| record          | SQL_DIAG_COLUMN_NUMBER         | Not Supported   | Returns SQL_COLUMN_NUMBER_UNKNOWN              |
| record          | SQL_DIAG_ROW_NUMBER            | Not Supported   | Returns SQL_ROW_NUMBER_UNKNOWN                 |