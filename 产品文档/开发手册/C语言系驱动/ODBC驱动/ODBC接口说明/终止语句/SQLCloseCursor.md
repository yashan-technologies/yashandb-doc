## 功能简介

关闭在语句句柄上打开的游标。

调用SQLFetch后必须调用该函数关闭当前正在Fetch的游标，否则调用其他函数时驱动程序管理器层会返回SQL_ERROR，并且无法使用诊断函数获取报错信息。

## 函数声明

```c
SQLRETURN SQLCloseCursor(  
     SQLHSTMT     StatementHandle);
```

## 参数说明

| 参数名               | 说明       |
| -------------------- | ---------- |
| StatementHandle (IN) | 语句句柄。 |



