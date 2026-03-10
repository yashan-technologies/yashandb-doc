## Functionality Introduction

Describes the columns of the result set, returning the column name, type, column size, decimal precision, and whether it can be null.

## Function Declaration

```c
SQLRETURN SQLDescribeCol(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   ColumnNumber,  
      SQLCHAR *      ColumnName,  
      SQLSMALLINT    BufferLength,  
      SQLSMALLINT *  NameLengthPtr,  
      SQLSMALLINT *  DataTypePtr,  
      SQLULEN *      ColumnSizePtr,  
      SQLSMALLINT *  DecimalDigitsPtr,  
      SQLSMALLINT *  NullablePtr);
```

## Parameter Description

|Parameter Name |Description |
| ---------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)    | The statement handle.                                       |
| ColumnNumber (IN)       | The column number of the result data, ordered by column order sequentially. |
| ColumnName (OUT)        | A pointer to a null-terminated cache that will return the column name. |
| BufferLength (IN)       | Length of the *ColumnName cache (in characters).          |
| NameLengthPtr (OUT)     | A pointer to a cache that will return the total number of characters that can be returned in *ColumnName. |
| DataTypePtr (OUT)       | A pointer to a cache where the SQL data type of the column will be returned. |
| ColumnSizePtr (OUT)     | A pointer to a cache that returns the size of the column in the data source (in characters). |
| DecimalDigitsPtr (OUT)  | A pointer to a cache that will return the number of decimal digits for the column in the data source. |
| NullablePtr (OUT)       | A pointer to a cache that will return a value indicating whether the column allows null values. |