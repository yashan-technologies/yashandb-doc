## 功能简介

返回语句中的参数个数。

## 函数声明

```c
SQLRETURN SQLNumParams(  
     SQLHSTMT        StatementHandle,  
     SQLSMALLINT *   ParameterCountPtr);
```

## 参数说明

| 参数名                  | 说明                                           |
| ----------------------- | ---------------------------------------------- |
| StatementHandle (IN)    | 语句句柄。                                     |
| ParameterCountPtr (OUT) | 指向要在其中返回语句中的参数个数的缓冲区的指针。 |

