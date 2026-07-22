## Functionality Introduction

Sets statement attributes.

## Function Declaration

```c
SQLRETURN SQLSetStmtAttr(  
     SQLHSTMT      StatementHandle,  
     SQLINTEGER    Attribute,  
     SQLPOINTER    ValuePtr,  
     SQLINTEGER    StringLength);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)    | Statement handle.                                         |
| Attribute (IN)          | The attribute to set.                                    |
| ValuePtr (IN)           | The value to associate with the Attribute.               |
| StringLength (IN)       | If the Attribute is an ODBC-defined attribute and ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |

Support status for the Attribute parameter:

|Attribute |Description |Support Status |
| ------------------------------ | ------------------------------------------------------------ | -------- |
| SQL_ATTR_PARAM_BIND_TYPE       | Support Row-Wise Binding and Column-Wise Binding            | Supported      |
| SQL_ATTR_PARAMSET_SIZE         | Number of parameter rows to bind at one time (default is 1) | Supported      |
| SQL_ATTR_ROW_ARRAY_SIZE        | Number of data rows to fetch at one time (default is 1)     | Supported      |
| SQL_ROWSET_SIZE                | Same as above                                              | Supported      |
| SQL_ATTR_ROW_BIND_TYPE         | Support Row-Wise Binding and Column-Wise Binding            | Supported      |
| SQL_ATTR_ROWS_FETCHED_PTR      | Pointer to the actual number of rows fetched at one time     | Supported      |
| SQL_ATTR_ROW_STATUS_PTR        | SQLUSMALLINT value array containing row status values after calling SQLFetch or SQLFetchScroll | Supported      |
| SQL_ATTR_APP_PARAM_DESC        |                                                           | Not Supported  |
| SQL_ATTR_APP_ROW_DESC          |                                                           | Not Supported  |
| SQL_ATTR_ASYNC_ENABLE          |                                                           | Not Supported  |
| SQL_ATTR_AUTO_IPD              |                                                           | Not Supported  |
| SQL_ATTR_CONCURRENCY           |                                                           | Not Supported  |
| SQL_ATTR_CURSOR_SCROLLABLE     |                                                           | Not Supported  |
| SQL_ATTR_CURSOR_SENSITIVITY    |                                                           | Not Supported  |
| SQL_ATTR_CURSOR_TYPE           |                                                           | Not Supported  |
| SQL_ATTR_ENABLE_AUTO_IPD       |                                                           | Not Supported  |
| SQL_ATTR_FETCH_BOOKMARK_PTR    |                                                           | Not Supported  |
| SQL_ATTR_IMP_PARAM_DESC        |                                                           | Not Supported  |
| SQL_ATTR_IMP_ROW_DESC          |                                                           | Not Supported  |
| SQL_ATTR_KEYSET_SIZE           |                                                           | Not Supported  |
| SQL_ATTR_MAX_LENGTH            |                                                           | Not Supported  |
| SQL_ATTR_MAX_ROWS              |                                                           | Not Supported  |
| SQL_ATTR_METADATA_ID           |                                                           | Not Supported  |
| SQL_ATTR_NOSCAN                |                                                           | Not Supported  |
| SQL_ATTR_PARAM_BIND_OFFSET_PTR  |                                                           | Not Supported  |
| SQL_ATTR_PARAM_OPERATION_PTR    |                                                           | Not Supported  |
| SQL_ATTR_PARAM_STATUS_PTR       |                                                           | Not Supported  |
| SQL_ATTR_PARAMS_PROCESSED_PTR   |                                                           | Not Supported  |
| SQL_ATTR_QUERY_TIMEOUT           |                                                           | Not Supported  |
| SQL_ATTR_RETRIEVE_DATA           |                                                           | Not Supported  |
| SQL_ATTR_ROW_BIND_OFFSET_PTR     |                                                           | Not Supported  |
| SQL_ATTR_ROW_NUMBER              |                                                           | Not Supported  |
| SQL_ATTR_ROW_OPERATION_PTR       |                                                           | Not Supported  |
| SQL_ATTR_SIMULATE_CURSOR         |                                                           | Not Supported  |
| SQL_ATTR_USE_BOOKMARKS           |                                                           | Not Supported  |