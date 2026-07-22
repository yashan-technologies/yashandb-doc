## Functionality Introduction

Sets connection attributes.

## Function Declaration:

```c
SQLRETURN SQLSetConnectAttr(  
     SQLHDBC       ConnectionHandle,  
     SQLINTEGER    Attribute,  
     SQLPOINTER    ValuePtr,  
     SQLINTEGER    StringLength);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN)   | Connection handle.                                       |
| Attribute (IN)          | The attribute to be set.                                |
| ValuePtr (IN)           | Pointer to the value to be associated with Attribute.  |
| StringLength (IN)       | If Attribute is an ODBC-defined attribute, and ValuePtr points to a string or binary cache, the length of this parameter should be *ValuePtr. |

Supported status of Attribute parameters:

|Attribute |Description |Support Status |
| --------------------------- | ---------------------------- | -------- |
| SQL_ATTR_AUTOCOMMIT         | Default is SQL_TRUE           | Supported      |
| SQL_ATTR_PACKET_SIZE        | Default is 128K               | Supported      |
| SQL_ATTR_TXN_ISOLATION      | Default is SQL_TXN_READ_COMMITTED | Supported      |
| SQL_ATTR_ACCESS_MODE        |                               | Not Supported   |
| SQL_ATTR_ASYNC_ENABLE       |                               | Not Supported   |
| SQL_ATTR_CONCURRENCY        |                               | Not Supported   |
| SQL_ATTR_CONNECTION_TIMEOUT |                               | Not Supported   |
| SQL_ATTR_CURRENT_CATALOG    |                               | Not Supported   |
| SQL_ATTR_CURSOR_SENSITIVITY |                               | Not Supported   |
| SQL_ATTR_CURSOR_TYPE        |                               | Not Supported   |
| SQL_ATTR_ENLIST_IN_DTC      |                               | Not Supported   |
| SQL_ATTR_FETCH_BOOKMARK_PTR |                               | Not Supported   |
| SQL_ATTR_KEYSET_SIZE        |                               | Not Supported   |
| SQL_ATTR_LOGIN_TIMEOUT      |                               | Not Supported   |
| SQL_ATTR_MAX_LENGTH         |                               | Not Supported   |
| SQL_ATTR_MAX_ROWS           |                               | Not Supported   |
| SQL_ATTR_METADATA_ID        |                               | Not Supported   |
| SQL_ATTR_NOSCAN             |                               | Not Supported   |
| SQL_ATTR_ODBC_CURSORS       |                               | Not Supported   |
| SQL_ATTR_QUERY_TIMEOUT      |                               | Not Supported   |
| SQL_ATTR_RETRIEVE_DATA      |                               | Not Supported   |
| SQL_ATTR_SIMULATE_CURSOR    |                               | Not Supported   |
| SQL_ATTR_USE_BOOKMARKS      |                               | Not Supported   |