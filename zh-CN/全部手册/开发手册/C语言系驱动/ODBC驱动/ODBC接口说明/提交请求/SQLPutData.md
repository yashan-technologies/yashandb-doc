## 函数用途

发送参数的一部分或全部数据值。

## ODBC函数原型

```c
SQLRETURN SQLPutData(  
      SQLHSTMT     StatementHandle,  
      SQLPOINTER   DataPtr,  
      SQLLEN       StrLen_or_Ind);
```

## 参数说明

|  参数名| 含义|
| -------------------- | ------------------------------------------ |
| StatementHandle (IN) | 语句句柄。                                 |
| DataPtr (IN)         | 指向包含参数或列的实际数据的缓冲区的指针。 |
| StrLen_or_Ind (IN)   | *DataPtr 的长度。                          |
