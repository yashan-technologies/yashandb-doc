## 功能简介

返回支持的驱动程序函数。

该函数与[SQLGetTypeInfo](SQLGetTypeInfo)不同，SQLGetTypeInfo采用ResultSet方式返回结果，而SQLGetFunctions函数所用的方式如下（以查询ODBC3驱动中SQLTABLES、SQLCOLUMNS和SQLSTATISTICS函数的参数的支持情况为例）：

```c
RETCODE retcodeTables, retcodeColumns, retcodeStatistics 
SQLUSMALLINT fExists[SQL_API_ODBC3_ALL_FUNCTIONS_SIZE]; 
   
retcode = SQLGetFunctions(hdbc, SQL_API_ODBC3_ALL_FUNCTIONS, fExists); 
if (reccode == SQL_SUCCESS &&  
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLTABLES) == SQL_TRUE && 
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLCOLUMNS) == SQL_TRUE && 
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLSTATISTICS) == SQL_TRUE)  
{ 
   // Continue with application 
}
```

## 函数声明

```c
SQLRETURN SQLGetFunctions(  
     SQLHDBC           ConnectionHandle,  
     SQLUSMALLINT      FunctionId,  
     SQLUSMALLINT *    SupportedPtr);
```

## 参数说明

| 参数名                | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN) | 连接句柄。                                                   |
| FunctionId (IN)       | 标识ODBC函数的#define值；SQL_API_ODBC3_ALL_FUNCTIONS或者SQL_API_ALL_FUNCTIONS。 |
| SupportedPtr  (OUT)   | 如果FunctionId标识单个ODBC函数，则SupportedPtr指向一个SQLUSMALLINT值，如果驱动程序支持指定函数，则SQL_TRUE；如果不支持，则SQL_FALSE。<br>如果functionId SQL_API_ODBC3_ALL_FUNCTIONS，则SupportedPtr指向元素数等于SQL_API_ODBC3_ALL_FUNCTIONS_SIZE的SQLSMALLINT数组。<br>如果FunctionId SQL_API_ALL_FUNCTIONS，则SupportedPtr指向包含100个元素的SQLUSMALLINT数组。 |

> **Note**：
>
> SQL_API_ODBC3_ALL_FUNCTIONS采用SQL_FUNC_EXISTS宏校验func支持情况。
>
> SQL_API_ALL_FUNCTIONS采用访问数组对应位置校验func支持情况。