## Functionality Overview

Returns the current values of multiple fields in the diagnostic record, including SQLSTATE, native error code, and diagnostic message text.

## Function Declaration

```c
SQLRETURN SQLGetDiagRec(  
     SQLSMALLINT     HandleType,  
     SQLHANDLE       Handle,  
     SQLSMALLINT     RecNumber,  
     SQLCHAR *       SQLState,  
     SQLINTEGER *    NativeErrorPtr,  
     SQLCHAR *       MessageText,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   TextLengthPtr);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------------------------------ |
| HandleType (IN)      | A handle type identifier that describes the type of handle to diagnose. |
| Handle (IN)          | A handle to the diagnostic data structure, of the type indicated by HandleType. |
| RecNumber (IN)       | Indicates the status record from which the application seeks information. |
| SQLState (OUT)       | A pointer to a cache that will return a five-character SQLSTATE code. |
| NativeErrorPtr (OUT) | A pointer to a cache that will return a data source-specific native error code. |
| MessageText (OUT)    | A pointer to a cache where the diagnostic message text string will be returned. |
| BufferLength (IN)    | The length of the *MessageText cache (in characters).     |
| TextLengthPtr (OUT)  | A pointer to a cache that returns the total number of characters. |