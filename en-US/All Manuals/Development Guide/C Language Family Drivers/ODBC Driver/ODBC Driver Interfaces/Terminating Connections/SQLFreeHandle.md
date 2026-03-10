## Functionality Introduction

Releases the environment, connection, statement, or descriptor handle.

## Function Declaration

```c
SQLRETURN SQLFreeHandle(  
     SQLSMALLINT   HandleType,  
     SQLHANDLE     Handle);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| HandleType (IN)   | The type of handle to be released by SQLFreeHandle. For detailed support information, please refer to [SQLAllocHandle](../Connecting to a Data Source/SQLAllocHandle). |
| Handle (IN)       | The handle to be released.                                  |