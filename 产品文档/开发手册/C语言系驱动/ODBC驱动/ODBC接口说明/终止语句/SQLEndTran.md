## 功能简介

提交或回滚事务。

## 函数声明

```c
SQLRETURN SQLEndTran(  
     SQLSMALLINT   HandleType,  
     SQLHANDLE     Handle,  
     SQLSMALLINT   CompletionType);
```

## 参数说明

| 参数名              | 说明                                          |
| ------------------- | --------------------------------------------- |
| HandleType (IN)     | 句柄类型标识符。                              |
| Handle (IN)         | HandleType 所指示类型的句柄，指示事务的范围。 |
| CompletionType (IN) | SQL_COMMIT或SQL_ROLLBACK。                    |

HandleType参数的支持情况：

| HandleType     | 说明                                   | 支持情况 |
| -------------- | -------------------------------------- | -------- |
| SQL_HANDLE_DBC | 提交或回滚当前连接的事务               | 支持     |
| SQL_HANDLE_ENV | 提交或回滚当前环境句柄下所有连接的事务 | 支持     |