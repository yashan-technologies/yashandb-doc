## Functionality Introduction

Returns a list of available data sources.

The current function is implemented for the driver manager, supporting both the Windows built-in driver manager and the Unix ODBC on Linux.

## Function Declaration

```c
SQLRETURN SQLDataSources(  
     SQLHENV          EnvironmentHandle,  
     SQLUSMALLINT     Direction,  
     SQLCHAR *        ServerName,  
     SQLSMALLINT      BufferLength1,  
     SQLSMALLINT *    NameLength1Ptr,  
     SQLCHAR *        Description,  
     SQLSMALLINT      BufferLength2,  
     SQLSMALLINT *    NameLength2Ptr);
```

## Parameter Description

|Parameter Name |Description |
| ------------ |---------------------------------------------------------------------------------------------------------------------------------------------------|
| EnvironmentHandle (IN) | Environment handle.                                                                                                                                           |
| Direction (IN)   | Determines which data source information the driver manager returns.                                                                                   |
| ServerName (OUT) | Pointer to the cache where the data source names will be returned.                                                                                     |
| BufferLength1 (IN) | Length of the *ServerName cache.                                                                                                                         |
| NameLength1Ptr (OUT) | Pointer to the cache that will return the total number of characters.                                                                                  |
| Description (OUT) | Pointer to the cache that will return the description of the driver associated with the data source.                                                    |
| BufferLength2 (IN) | Length of the *Description cache (in characters).                                                                                                       |
| NameLength2Ptr (OUT) | Pointer to the cache that will return the total number of characters.                                                                                  |