## 功能简介

执行语句。

## 函数声明

```c
SQLRETURN SQLExecDirect(  
     SQLHSTMT     StatementHandle,  
     SQLCHAR *    StatementText,  
     SQLINTEGER   TextLength);
```

## 参数说明

| 参数名               | 说明                                    |
| -------------------- | --------------------------------------- |
| StatementHandle (IN) | 语句句柄。                              |
| StatementText (IN)   | 要执行的SQL语句。                       |
| TextLength (IN)      | *StatementText 的长度（以字符为单位）。 |