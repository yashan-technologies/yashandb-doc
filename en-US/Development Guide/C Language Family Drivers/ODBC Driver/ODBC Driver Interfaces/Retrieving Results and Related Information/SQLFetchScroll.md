## Functionality Overview

Returns scrollable result rows.

## ODBC Function Prototype

```c
SQLRETURN SQLFetchScroll(  
      SQLHSTMT      StatementHandle,  
      SQLSMALLINT   FetchOrientation,  
      SQLLEN        FetchOffset);
```

## Parameter Description

|Parameter Name |Meaning |
| --------------------- | -------------- |
| StatementHandle (IN)   | Statement handle. |
| FetchOrientation (IN)  | Fetch type.     |
| FetchOffset (IN)       | Number of rows to fetch. |

## Additional Notes

1. Currently, FetchOrientation only supports SQL_FETCH_NEXT.

2. Currently, FetchOffset only supports 0.