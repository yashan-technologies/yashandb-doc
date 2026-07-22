## 功能简介

返回存储在特定数据源中的表名和列表。 

## 函数声明

```c
SQLRETURN SQLTables(  
     SQLHSTMT       StatementHandle,  
     SQLCHAR *      CatalogName,  
     SQLSMALLINT    NameLength1,  
     SQLCHAR *      SchemaName,  
     SQLSMALLINT    NameLength2,  
     SQLCHAR *      TableName,  
     SQLSMALLINT    NameLength3,  
     SQLCHAR *      TableType,  
     SQLSMALLINT    NameLength4);
```

## 参数说明

|  参数名| 说明|
| -------------------- | ------------------------------------- |
| StatementHandle (IN) | 结果集的语句句柄。                    |
| CatalogName (IN)     | 目录名称。                            |
| NameLength1 (IN)     | *CatalogName 的长度（以字符为单位）。 |
| SchemaName (IN)      | 架构名称的字符串搜索模式。            |
| NameLength2 (IN)     | 长度（以 *SchemaName 的字符为单位）。 |
| TableName (IN)       | 表名的字符串搜索模式。                |
| NameLength3 (IN)     | *TableName 的长度（以字符为单位）。   |
| TableType (IN)       | 要匹配的表类型的列表。                |
| NameLength4 (IN)     | *TableType 的长度（以字符为单位）。   |

TableType参数的支持情况：

|  TableType| 说明| 支持情况|
| ---------------- | ---------- | -------- |
| TABLE            | 表         | 支持     |
| VIEW             | 视图       | 支持     |
| SYSTEM TABLE     | 系统表     | 不支持   |
| GLOBAL TEMPORARY | 全局临时表 | 不支持   |
| LOCAL TEMPORARY  | 本地临时表 | 不支持   |
| ALIAS            | 别名       | 不支持   |
| SYNONYM          | 同义词     | 不支持   |
