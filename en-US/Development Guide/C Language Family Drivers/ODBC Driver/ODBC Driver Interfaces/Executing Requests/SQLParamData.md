## Function Purpose

Used in conjunction with SQLPutData to provide parameter data during execution.

## ODBC Function Prototype

```c
SQLRETURN SQLParamData(  
     SQLHSTMT       StatementHandle,  
     SQLPOINTER *   ValuePtrPtr);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)  | Statement handle.                                              |
| ValuePtrPtr (IN)      | A pointer to the cache that will return the address of the *ParameterValuePtr* cache specified in SQLBindParameter. |