## Functionality Introduction

Returns the table names and list stored in a specific data source.

## Function Declaration

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

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------- |
| StatementHandle (IN) | The statement handle for the result set. |
| CatalogName (IN)     | The catalog name.                    |
| NameLength1 (IN)     | The length of *CatalogName (in characters). |
| SchemaName (IN)      | The string search pattern for the schema name. |
| NameLength2 (IN)     | The length (in characters of *SchemaName). |
| TableName (IN)       | The string search pattern for the table name. |
| NameLength3 (IN)     | The length of *TableName (in characters). |
| TableType (IN)       | The list of table types to match.    |
| NameLength4 (IN)     | The length of *TableType (in characters). |

Support for TableType parameter:

|TableType |Description |Support Status |
| ---------------- | ---------- | -------- |
| TABLE            | Table                       | Supported       |
| VIEW             | View                        | Supported       |
| SYSTEM TABLE     | System table                | Not supported    |
| GLOBAL TEMPORARY | Global temporary table      | Not supported    |
| LOCAL TEMPORARY  | Local temporary table       | Not supported    |
| ALIAS            | Alias                       | Not supported    |
| SYNONYM          | Synonym                     | Not supported    |