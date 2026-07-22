## Functionality Introduction

Connect to a specific driver by displaying the user's connection dialog through a connection string or by requesting the driver manager and driver.

## Function Declaration

```c
SQLRETURN SQLDriverConnect(  
     SQLHDBC         ConnectionHandle,  
     SQLHWND         WindowHandle,  
     SQLCHAR *       InConnectionString,  
     SQLSMALLINT     StringLength1,  
     SQLCHAR *       OutConnectionString,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   StringLength2Ptr,  
     SQLUSMALLINT    DriverCompletion);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | -------------------------------------- |
| ConnectionHandle (IN)   | Connection handle.                        |
| WindowHandle (IN)       | Window handle.                           |
| InConnectionString (IN) | Syntax in the full connection string, partial connection string, or empty string. |
| StringLength1 (IN)      | Length of *InConnectionString.          |
| OutConnectionString (OUT)| Pointer to the completed connection string cache.  |
| BufferLength (IN)       | Length of *OutConnectionString cache (in characters). |
| StringLength2Ptr (OUT)  | Pointer to the cache that will return the total character count, which can be returned in *OutConnectionString. |
| DriverCompletion (IN)   | Flag indicating whether the driver manager or driver must prompt for more connection information. |

DriverCompletion parameter support:

|DriverCompletion |Description |Support Status |
| ---------------------------- | ------------------------------------------------------------ | -------- |
| SQL_DRIVER_COMPLETE          | This option attempts to log in using existing information first; if login fails, a prompt appears asking the user to enter missing information. | Supported      |
| SQL_DRIVER_COMPLETE_REQUIRED | This option attempts to log in using existing information first; if login fails, a prompt appears asking the user to enter missing information (same as above). | Supported      |
| SQL_DRIVER_NOPROMPT          | No prompt window                                               | Supported      |
| SQL_DRIVER_PROMPT            | With prompt window                                            | Supported      |