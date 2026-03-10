## Functionality Introduction

Returns the value of the connection attribute.

## Function Declaration

```c
SQLRETURN SQLGetConnectAttr(  
     SQLHDBC        ConnectionHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN) | Connection handle.                                           |
| Attribute (IN)        | The attribute to retrieve.                                   |
| ValuePtr (OUT)       | A pointer to memory where the current value of the feature specified by Attribute will be returned. |
| BufferLength (IN)     | If Attribute is an ODBC-defined property and ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |
| StringLengthPtr (OUT) | A pointer to the cache where the total number of bytes to be returned will be stored. |

Support status for the Attribute parameter:

|Attribute |Description |Support Status |
| --------------------------- | ---------------------------- | -------- |
| SQL_ATTR_AUTOCOMMIT         | Default is SQL_TRUE            | Supported      |
| SQL_ATTR_PACKET_SIZE        | Default is 128K                | Supported      |
| SQL_ATTR_TXN_ISOLATION      | Default is SQL_TXN_READ_COMMITTED | Supported      |
| SQL_ATTR_ACCESS_MODE        | Returns SQL_MODE_READ_WRITE    | Not supported   |
| SQL_ATTR_AUTO_IPD           | Returns SQL_FALSE              | Not supported   |
| SQL_ATTR_CONNECTION_DEAD    |                                | Not supported   |
| SQL_ATTR_CONNECTION_TIMEOUT  |                                | Not supported   |
| SQL_ATTR_CURRENT_CATALOG     | Returns "YashanDB"            | Not supported   |
| SQL_ATTR_ODBC_CURSORS       | Returns SQL_CUR_USE_IF_NEEDED  | Not supported   |