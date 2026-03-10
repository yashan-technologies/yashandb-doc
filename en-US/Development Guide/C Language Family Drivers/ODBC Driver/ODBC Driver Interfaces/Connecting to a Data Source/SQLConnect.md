## Functionality Overview

Connect to a specific driver using the data source name, user ID, and password.

## Function Declaration

```c
SQLRETURN SQLConnect(  
     SQLHDBC        ConnectionHandle,  
     SQLCHAR *      ServerName,  
     SQLSMALLINT    NameLength1,  
     SQLCHAR *      UserName,  
     SQLSMALLINT    NameLength2,  
     SQLCHAR *      Authentication,  
     SQLSMALLINT    NameLength3);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | -------------------------------------- |
| ConnectionHandle (IN) | Connection handle.                     |
| ServerName (IN)       | Data source name.                      |
| NameLength1 (IN)      | Length of *ServerName (in characters).|
| UserName (IN)         | User identifier.                       |
| NameLength2 (IN)      | Length of *UserName (in characters).  |
| Authentication (IN)   | Authentication string (password).     |
| NameLength3 (IN)      | Length of Authentication (in characters). |