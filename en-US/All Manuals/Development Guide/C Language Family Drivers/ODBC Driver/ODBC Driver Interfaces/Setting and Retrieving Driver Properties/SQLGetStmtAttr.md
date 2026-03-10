## Functionality Overview

Returns the values of statement attributes.

## Function Declaration

```c
SQLRETURN SQLGetStmtAttr(  
     SQLHSTMT        StatementHandle,  
     SQLINTEGER      Attribute,  
     SQLPOINTER      ValuePtr,  
     SQLINTEGER      BufferLength,  
     SQLINTEGER *    StringLengthPtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)   | The statement handle.                                        |
| Attribute (IN)         | The attribute to retrieve.                                   |
| ValuePtr (OUT)         | A pointer to a cache where the current value of the feature specified by Attribute is returned. |
| BufferLength (IN)      | If ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |
| StringLengthPtr (OUT)  | A pointer to a cache, where the total number of bytes to be returned. |

Support for Attribute parameters:

|Attribute |Description |Support Status |
| ------------------------------ | ------------------------------ | -------- |
| SQL_ATTR_APP_PARAM_DESC        | The only way to retrieve implicit APD handle | Supported       |
| SQL_ATTR_APP_ROW_DESC          | The only way to retrieve implicit ARD handle | Supported       |
| SQL_ATTR_IMP_PARAM_DESC        | The only way to retrieve implicit IPD handle | Supported       |
| SQL_ATTR_IMP_ROW_DESC          | The only way to retrieve implicit IRD handle | Supported       |
| SQL_ATTR_PARAMSET_SIZE         | Number of parameter rows bound at once (default is 1) | Supported       |
| SQL_ATTR_ROW_ARRAY_SIZE        | Number of data rows fetched at once (default is 1) | Supported       |
| SQL_ROWSET_SIZE                | Number of data rows fetched at once (default is 1) | Supported       |
| SQL_ATTR_ROWS_FETCHED_PTR      | Pointer to the actual number of rows fetched | Supported       |
| SQL_ATTR_AUTO_IPD              | Returns SQL_FALSE                    | Not Supported   |
| SQL_ATTR_CURSOR_SCROLLABLE     | Returns SQL_NONSCROLLABLE            | Not Supported   |
| SQL_ATTR_PARAM_BIND_OFFSET_PTR  |                                       | Not Supported   |
| SQL_ATTR_PARAM_BIND_TYPE       | Returns SQL_BIND_BY_COLUMN           | Not Supported   |
| SQL_ATTR_PARAM_OPERATION_PTR   |                                       | Not Supported   |
| SQL_ATTR_PARAM_STATUS_PTR      |                                       | Not Supported   |
| SQL_ATTR_PARAMS_PROCESSED_PTR  |                                       | Not Supported   |
| SQL_ATTR_ROW_BIND_OFFSET_PTR   |                                       | Not Supported   |
| SQL_ATTR_ROW_BIND_TYPE         | Returns SQL_BIND_BY_COLUMN           | Not Supported   |
| SQL_ATTR_ROW_NUMBER            | Returns 0                            | Not Supported   |
| SQL_ATTR_ROW_OPERATION_PTR     |                                       | Not Supported   |
| SQL_ATTR_ROW_STATUS_PTR        |                                       | Not Supported   |
| SQL_ATTR_SIMULATE_CURSOR       |                                       | Not Supported   |