## 功能简介

返回语句属性的值。

## 函数声明

```c
SQLRETURN SQLGetStmtAttr(  
     SQLHSTMT        StatementHandle,  
     SQLINTEGER      Attribute,  
     SQLPOINTER      ValuePtr,  
     SQLINTEGER      BufferLength,  
     SQLINTEGER *    StringLengthPtr);
```

## 参数说明

| 参数名                | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| StatementHandle (IN) | 语句句柄。                                         |
| Attribute (IN)         | 要检索的属性。                                              |
| ValuePtr (OUT)    | 指向缓冲区的指针，在该缓冲区中返回由 Attribute 指定的特性的当前值。 |
| BufferLength (IN)    | 如果 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区要返回的总字节数。 |

Attribute参数的支持情况：

| Attribute                      | 说明                           | 支持情况 |
| ------------------------------ | ------------------------------ | -------- |
| SQL_ATTR_APP_PARAM_DESC        | 获取隐式APD句柄的唯一方式      | 支持     |
| SQL_ATTR_APP_ROW_DESC          | 获取隐式ARD句柄的唯一方式      | 支持     |
| SQL_ATTR_IMP_PARAM_DESC        | 获取隐式IPD句柄的唯一方式      | 支持     |
| SQL_ATTR_IMP_ROW_DESC          | 获取隐式IRD句柄的唯一方式      | 支持     |
| SQL_ATTR_PARAMSET_SIZE         | 单次绑定参数行数（默认为1）    | 支持     |
| SQL_ATTR_ROW_ARRAY_SIZE        | 单次取数据行数（默认为1）      | 支持     |
| SQL_ROWSET_SIZE                | 单次取数据行数（默认为1）      | 支持     |
| SQL_ATTR_ROWS_FETCHED_PTR      | 单次取数据实际取到的行数的指针 | 支持     |
| SQL_ATTR_AUTO_IPD              | 返回SQL_FALSE                  | 不支持   |
| SQL_ATTR_CURSOR_SCROLLABLE     | 返回SQL_NONSCROLLABLE          | 不支持   |
| SQL_ATTR_PARAM_BIND_OFFSET_PTR |                                | 不支持   |
| SQL_ATTR_PARAM_BIND_TYPE       | 返回SQL_BIND_BY_COLUMN         | 不支持   |
| SQL_ATTR_PARAM_OPERATION_PTR   |                                | 不支持   |
| SQL_ATTR_PARAM_STATUS_PTR      |                                | 不支持   |
| SQL_ATTR_PARAMS_PROCESSED_PTR  |                                | 不支持   |
| SQL_ATTR_ROW_BIND_OFFSET_PTR   |                                | 不支持   |
| SQL_ATTR_ROW_BIND_TYPE         | 返回SQL_BIND_BY_COLUMN         | 不支持   |
| SQL_ATTR_ROW_NUMBER            | 返回0                          | 不支持   |
| SQL_ATTR_ROW_OPERATION_PTR     |                                | 不支持   |
| SQL_ATTR_ROW_STATUS_PTR        |                                | 不支持   |
| SQL_ATTR_SIMULATE_CURSOR       |                                | 不支持   |
