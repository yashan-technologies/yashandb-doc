## 功能简介

结束语句处理，放弃挂起的结果，并释放与语句句柄关联的所有资源。

## 函数声明

```c
SQLRETURN SQLFreeStmt(  
     SQLHSTMT       StatementHandle,  
     SQLUSMALLINT   Option);
```

## 参数说明

|  参数名| 说明|
| -------------------- | ---------- |
| StatementHandle (IN) | 语句句柄。 |
| Option (IN)          | 可选的值。 |

Option参数的支持情况：

|  Option| 说明| 支持情况|
| ---------------- | ------------------------------------------------------------ | -------- |
| SQL_ CLOSE       | 同SQLCloseCursor                                             | 支持     |
| SQL_UNBIND       | 将ARD的SQL_DESC_COUNT字段设置为 0，释放由SQLBindCol绑定的所有列缓冲区以用于给定StatementHandle。 | 支持     |
| SQL_RESET_PARAMS | 将APD的SQL_DESC_COUNT字段设置为 0，释放SQLBindParameter为给定StatementHandle设置的所有参数缓冲区。 | 支持     |
| SQL_DROP         | 已弃用                                                       | 不支持   |
