## Functionality Overview

Returns information about specific drivers and data sources.

The database support information returned by this function may not reflect the latest version. For the latest version information, please contact our technical support.

## Function Declaration

```c
SQLRETURN SQLGetInfo(  
     SQLHDBC         ConnectionHandle,  
     SQLUSMALLINT    InfoType,  
     SQLPOINTER      InfoValuePtr,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   StringLengthPtr);
```

## Parameters Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN)  | Connection handle.                                         |
| InfoType (IN)         | Information type.                                         |
| InfoValuePtr (OUT)    | Pointer to the cache to return information.               |
| BufferLength (OUT)    | Length of the cache pointed to by *InfoValuePtr. If the value in *InfoValuePtr is not a string, or if *InfoValuePtr is a null pointer, the BufferLength parameter is ignored. |
| StringLengthPtr (OUT) | Pointer to the cache that returns the total number of bytes (excluding the null termination characters for character data) that can be returned in *InfoValuePtr. |