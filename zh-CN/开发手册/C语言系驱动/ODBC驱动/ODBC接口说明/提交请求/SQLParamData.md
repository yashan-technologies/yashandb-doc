## 功能简介

与SQLPutData结合使用 ，在执行时提供参数数据。

## ODBC函数原型

```c
SQLRETURN SQLParamData(  
     SQLHSTMT       StatementHandle,  
     SQLPOINTER *   ValuePtrPtr);
```

## 参数说明

|  参数名| 含义|
| -------------------- | ------------------------------------------------------------ |
| StatementHandle (IN) | 语句句柄。                                                   |
| ValuePtrPtr (IN)     | 指向缓冲区的指针，该缓冲区将返回 SQLBindParameter 中指定的 *ParameterValuePtr* 缓冲区的地址。 |
