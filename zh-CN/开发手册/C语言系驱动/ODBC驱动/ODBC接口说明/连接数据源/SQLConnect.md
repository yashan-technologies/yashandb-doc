## 功能简介

按数据源名称、用户 ID 和密码连接到特定驱动程序。

## 函数声明

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

## 参数说明

|  参数名| 说明|
| --------------------- | -------------------------------------- |
| ConnectionHandle (IN) | 连接句柄。                             |
| ServerName (IN)       | 数据源名称。                           |
| NameLength1 (IN)      | *ServerName 的长度（以字符为单位）。   |
| UserName (IN)         | 用户标识符。                           |
| NameLength2 (IN)      | *UserName 的长度（以字符为单位）。     |
| Authentication (IN)   | 身份验证字符串（密码）。               |
| NameLength3 (IN)      | Authentication的长度（以字符为单位）。 |
