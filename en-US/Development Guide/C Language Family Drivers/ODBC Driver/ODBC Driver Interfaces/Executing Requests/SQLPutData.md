## Functionality Overview

Sends part or all of the parameter data values.

## ODBCFunction Prototype

```c
SQLRETURN SQLPutData(  
      SQLHSTMT     StatementHandle,  
      SQLPOINTER   DataPtr,  
      SQLLEN       StrLen_or_Ind);
```

## Parameter Description

|Parameter Name |Meaning |
| -------------------- | ------------------------------------------ |
| StatementHandle (IN)  | Statement handle.                           |
| DataPtr (IN)         | Pointer to the cache that contains the actual data of the parameter or column. |
| StrLen_or_Ind (IN)   | Length of *DataPtr.                        |