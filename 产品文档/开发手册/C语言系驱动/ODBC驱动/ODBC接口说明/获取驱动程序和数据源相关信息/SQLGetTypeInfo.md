## 功能简介

返回有关数据源支持的数据类型的信息。

## 函数声明

```c
SQLRETURN SQLGetTypeInfo(  
     SQLHSTMT      StatementHandle,  
     SQLSMALLINT   DataType);
```

## 参数说明

| 参数名               | 说明               |
| -------------------- | ------------------ |
| StatementHandle (IN) | 结果集的语句句柄。 |
| DataType (IN)        | SQL 数据类型。     |

