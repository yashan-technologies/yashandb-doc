## 功能简介

准备一个SQL语句待之后执行。

## 函数声明

```c
SQLRETURN SQLPrepare(  
     SQLHSTMT      StatementHandle,  
     SQLCHAR *     StatementText,  
     SQLINTEGER    TextLength);
```

## 参数说明

| 参数名               | 说明                                    |
| -------------------- | --------------------------------------- |
| StatementHandle (IN) | 语句句柄。                              |
| StatementText (IN)   | 要执行的 SQL 语句。                     |
| TextLength (IN)      | *StatementText 的长度（以字符为单位）。 |