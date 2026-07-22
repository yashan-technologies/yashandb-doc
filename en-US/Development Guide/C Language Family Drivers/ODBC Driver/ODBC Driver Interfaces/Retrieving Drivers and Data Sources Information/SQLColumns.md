## Functionality Overview

Returns a list of column names from a specified table.

## ODBC Function Prototype

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

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------- |
| StatementHandle (IN) | The statement handle for the result set. |
| CatalogName (IN)     | The catalog name.                    |
| NameLength1 (IN)     | Length of *CatalogName (in characters). |
| SchemaName (IN)      | The string search pattern for the schema name. |
| NameLength2 (IN)     | Length (in characters of *SchemaName). |
| TableName (IN)       | The string search pattern for the table name. |
| NameLength3 (IN)     | Length of *TableName (in characters). |
| ColumnName (IN)      | The string search pattern for the column name. |
| NameLength4 (IN)     | Length of *ColumnName (in characters). |

## Additional Description