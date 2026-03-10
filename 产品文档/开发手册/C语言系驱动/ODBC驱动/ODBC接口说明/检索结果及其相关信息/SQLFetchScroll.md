## 函数用途

返回可滚动的结果行。

## ODBC函数原型

```c
SQLRETURN SQLFetchScroll(  
      SQLHSTMT      StatementHandle,  
      SQLSMALLINT   FetchOrientation,  
      SQLLEN        FetchOffset);
```

## 参数说明

| 参数名                | 含义           |
| --------------------- | -------------- |
| StatementHandle (IN)  | 语句句柄。     |
| FetchOrientation (IN) | 提取类型。     |
| FetchOffset (IN)      | 要提取的行数。 |

## 其他说明

1、目前FetchOrientation仅支持SQL_FETCH_NEXT。

2、目前FetchOffset仅支持0。