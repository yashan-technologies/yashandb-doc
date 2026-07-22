## 功能简介

返回受插入、更新或删除请求影响的行数。

## 函数声明

```c
SQLRETURN SQLRowCount(  
      SQLHSTMT   StatementHandle,  
      SQLLEN *   RowCountPtr);
```

## 参数说明

|  参数名| 说明|
| -------------------- | -------------------------------- |
| StatementHandle (IN) | 语句句柄。                       |
| RowCountPtr (OUT)    | 指向要在其中返回行计数的缓冲区。 |
