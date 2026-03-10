## Functionality Introduction

Get environment, connection, statement, or descriptor handles.

## Function Declaration

```c
SQLRETURN SQLAllocHandle(  
      SQLSMALLINT   HandleType,  
      SQLHANDLE     InputHandle,  
      SQLHANDLE *   OutputHandlePtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | -------------------------------------------------------- |
| HandleType (IN)       | The type of handle to be allocated by SQLAllocHandle. |
| InputHandle (IN)      | The input handle for which a new handle will be allocated. |
| OutputHandlePtr (OUT) | A pointer to cache that will return the handle to the newly allocated data structure. |

Support for the HandleType parameter:

|HandleType |Description |Support Status |
| ------------------------- | -------------------------------- | -------- |
| SQL_HANDLE_DBC            |                                    | Supported        |
| SQL_HANDLE_DBC_INFO_TOKEN | Only called by the driver, not recognized by ODBC applications | Not Supported     |
| SQL_HANDLE_DESC           | Descriptor creation is currently not supported | Not Supported     |
| SQL_HANDLE_ENV            |                                    | Supported        |
| SQL_HANDLE_STMT           |                                    | Supported        |