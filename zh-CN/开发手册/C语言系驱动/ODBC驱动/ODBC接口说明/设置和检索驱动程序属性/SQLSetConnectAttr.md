## 功能简介

设置连接属性。

## 函数声明：

```c
SQLRETURN SQLSetConnectAttr(  
     SQLHDBC       ConnectionHandle,  
     SQLINTEGER    Attribute,  
     SQLPOINTER    ValuePtr,  
     SQLINTEGER    StringLength);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN) | 连接句柄。                                                   |
| Attribute (IN)        | 要设置的属性。                                               |
| ValuePtr (IN)         | 指向要与 Attribute 关联的值的指针。                          |
| StringLength (IN)     | 如果 Attribute 是 ODBC 定义的属性，并且 ValuePtr 指向字符串或二进制缓冲区，则此参数的长度应为 *ValuePtr。 |

Attribute参数的支持情况：

|  Attribute| 说明| 支持情况|
| --------------------------- | ---------------------------- | -------- |
| SQL_ATTR_AUTOCOMMIT         | 默认是SQL_TRUE               | 支持     |
| SQL_ATTR_PACKET_SIZE        | 默认是128K                   | 支持     |
| SQL_ATTR_TXN_ISOLATION      | 默认是SQL_TXN_READ_COMMITTED | 支持     |
| SQL_ATTR_ACCESS_MODE        |                              | 不支持   |
| SQL_ATTR_ASYNC_ENABLE       |                              | 不支持   |
| SQL_ATTR_CONCURRENCY        |                              | 不支持   |
| SQL_ATTR_CONNECTION_TIMEOUT |                              | 不支持   |
| SQL_ATTR_CURRENT_CATALOG    |                              | 不支持   |
| SQL_ATTR_CURSOR_SENSITIVITY |                              | 不支持   |
| SQL_ATTR_CURSOR_TYPE        |                              | 不支持   |
| SQL_ATTR_ENLIST_IN_DTC      |                              | 不支持   |
| SQL_ATTR_FETCH_BOOKMARK_PTR |                              | 不支持   |
| SQL_ATTR_KEYSET_SIZE        |                              | 不支持   |
| SQL_ATTR_LOGIN_TIMEOUT      |                              | 不支持   |
| SQL_ATTR_MAX_LENGTH         |                              | 不支持   |
| SQL_ATTR_MAX_ROWS           |                              | 不支持   |
| SQL_ATTR_METADATA_ID        |                              | 不支持   |
| SQL_ATTR_NOSCAN             |                              | 不支持   |
| SQL_ATTR_ODBC_CURSORS       |                              | 不支持   |
| SQL_ATTR_QUERY_TIMEOUT      |                              | 不支持   |
| SQL_ATTR_RETRIEVE_DATA      |                              | 不支持   |
| SQL_ATTR_SIMULATE_CURSOR    |                              | 不支持   |
| SQL_ATTR_USE_BOOKMARKS      |                              | 不支持   |
