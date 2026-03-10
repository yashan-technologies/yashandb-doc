## 功能简介

返回结果集中的列数。

## 函数声明

```c
SQLRETURN SQLNumResultCols(  
     SQLHSTMT        StatementHandle,  
     SQLSMALLINT *   ColumnCountPtr);
```

## 参数说明

|  参数名| 说明|
| -------------------- | ------------------------------------------------ |
| StatementHandle (IN) | 语句句柄。                                       |
| ColumnCountPtr (OUT) | 指向缓冲区的指针，该缓冲区将返回结果集中的列数。 |
