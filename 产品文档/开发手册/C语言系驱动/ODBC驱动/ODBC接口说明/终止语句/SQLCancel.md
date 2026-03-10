## 功能简介

取消当前语句执行，如果当前语句已执行完毕或不处于执行状态，则返回SQL_SUCCESS。

## 函数声明

```c
SQLRETURN SQLCancel(  
     SQLHSTMT     StatementHandle);
```

## 参数说明

| 参数名               | 说明       |
| -------------------- | ---------- |
| StatementHandle (IN) | 语句句柄。 |

