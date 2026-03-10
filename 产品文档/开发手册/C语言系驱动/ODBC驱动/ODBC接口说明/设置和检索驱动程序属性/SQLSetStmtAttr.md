## 功能简介

设置语句属性。

## 函数声明

```c
SQLRETURN SQLSetStmtAttr(  
     SQLHSTMT      StatementHandle,  
     SQLINTEGER    Attribute,  
     SQLPOINTER    ValuePtr,  
     SQLINTEGER    StringLength);
```

## 参数说明

| 参数名                | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| StatementHandle (IN) | 语句句柄。                                         |
| Attribute (IN)         | 要设置的属性。                                        |
| ValuePtr (IN)  | 要与 Attribute 关联的值。 |
| StringLength (IN) | 如果 Attribute 是 ODBC 定义的属性，并且 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |

Attribute参数的支持情况：

| Attribute                      | 说明                                                         | 支持情况 |
| ------------------------------ | ------------------------------------------------------------ | -------- |
| SQL_ATTR_PARAM_BIND_TYPE       | 返回SQL_BIND_BY_COLUMN                                       | 支持     |
| SQL_ATTR_PARAMSET_SIZE         | 单次绑定参数行数（默认为1）                                  | 支持     |
| SQL_ATTR_ROW_ARRAY_SIZE        | 单次取数据行数（默认为1）                                    | 支持     |
| SQL_ROWSET_SIZE                | 同上                                                         | 支持     |
| SQL_ATTR_ROW_BIND_TYPE         | 返回SQL_BIND_BY_COLUMN                                       | 支持     |
| SQL_ATTR_ROWS_FETCHED_PTR      | 单次取数据实际取到的行数的指针                               | 支持     |
| SQL_ATTR_ROW_STATUS_PTR        | 调用SQLFetch或SQLFetchScroll后包含行状态值的SQLUSMALLINT值数组 | 支持     |
| SQL_ATTR_APP_PARAM_DESC        |                                                              | 不支持   |
| SQL_ATTR_APP_ROW_DESC          |                                                              | 不支持   |
| SQL_ATTR_ASYNC_ENABLE          |                                                              | 不支持   |
| SQL_ATTR_AUTO_IPD              |                                                              | 不支持   |
| SQL_ATTR_CONCURRENCY           |                                                              | 不支持   |
| SQL_ATTR_CURSOR_SCROLLABLE     |                                                              | 不支持   |
| SQL_ATTR_CURSOR_SENSITIVITY    |                                                              | 不支持   |
| SQL_ATTR_CURSOR_TYPE           |                                                              | 不支持   |
| SQL_ATTR_ENABLE_AUTO_IPD       |                                                              | 不支持   |
| SQL_ATTR_FETCH_BOOKMARK_PTR    |                                                              | 不支持   |
| SQL_ATTR_IMP_PARAM_DESC        |                                                              | 不支持   |
| SQL_ATTR_IMP_ROW_DESC          |                                                              | 不支持   |
| SQL_ATTR_KEYSET_SIZE           |                                                              | 不支持   |
| SQL_ATTR_MAX_LENGTH            |                                                              | 不支持   |
| SQL_ATTR_MAX_ROWS              |                                                              | 不支持   |
| SQL_ATTR_METADATA_ID           |                                                              | 不支持   |
| SQL_ATTR_NOSCAN                |                                                              | 不支持   |
| SQL_ATTR_PARAM_BIND_OFFSET_PTR |                                                              | 不支持   |
| SQL_ATTR_PARAM_OPERATION_PTR   |                                                              | 不支持   |
| SQL_ATTR_PARAM_STATUS_PTR      |                                                              | 不支持   |
| SQL_ATTR_PARAMS_PROCESSED_PTR  |                                                              | 不支持   |
| SQL_ATTR_QUERY_TIMEOUT         |                                                              | 不支持   |
| SQL_ATTR_RETRIEVE_DATA         |                                                              | 不支持   |
| SQL_ATTR_ROW_BIND_OFFSET_PTR   |                                                              | 不支持   |
| SQL_ATTR_ROW_NUMBER            |                                                              | 不支持   |
| SQL_ATTR_ROW_OPERATION_PTR     |                                                              | 不支持   |
| SQL_ATTR_SIMULATE_CURSOR       |                                                              | 不支持   |
| SQL_ATTR_SIMULATE_CURSOR       |                                                              | 不支持   |
| SQL_ATTR_USE_BOOKMARKS         |                                                              | 不支持   |