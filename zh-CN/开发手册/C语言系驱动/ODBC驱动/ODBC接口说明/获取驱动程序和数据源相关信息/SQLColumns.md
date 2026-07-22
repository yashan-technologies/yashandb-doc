## 功能简介

返回指定表中的列名的列表。

## ODBC函数原型

```c
SQLRETURN SQLColumns(  
     SQLHSTMT       StatementHandle,  
     SQLCHAR *      CatalogName,  
     SQLSMALLINT    NameLength1,  
     SQLCHAR *      SchemaName,  
     SQLSMALLINT    NameLength2,  
     SQLCHAR *      TableName,  
     SQLSMALLINT    NameLength3,  
     SQLCHAR *      ColumnName,  
     SQLSMALLINT    NameLength4);
```

## 参数说明

|  参数名| 含义|
| -------------------- | ------------------------------------- |
| StatementHandle (IN) | 结果集的语句句柄。                    |
| CatalogName (IN)     | 目录名称。                            |
| NameLength1 (IN)     | *CatalogName 的长度（以字符为单位）。 |
| SchemaName (IN)      | 架构名称的字符串搜索模式。            |
| NameLength2 (IN)     | 长度（以 *SchemaName 的字符为单位）。 |
| TableName (IN)       | 表名的字符串搜索模式。                |
| NameLength3 (IN)     | *TableName 的长度（以字符为单位）。   |
| ColumnName (IN)      | 列名的字符串搜索模式。                |
| NameLength4 (IN)     | *ColumnName 的长度（以字符为单位）。  |

## 其他说明