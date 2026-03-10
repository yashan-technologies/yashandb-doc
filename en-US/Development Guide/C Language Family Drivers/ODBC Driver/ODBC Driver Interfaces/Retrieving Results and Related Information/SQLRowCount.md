## Introduction to Functionality

Returns the number of rows affected by an INSERT, UPDATE, or DELETE request.

## Function Declaration

```c
SQLRETURN SQLRowCount(  
      SQLHSTMT   StatementHandle,  
      SQLLEN *   RowCountPtr);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | -------------------------------- |
| StatementHandle (IN)   | Statement handle.                  |
| RowCountPtr (OUT)      | A pointer to the cache where the row count will be returned. |