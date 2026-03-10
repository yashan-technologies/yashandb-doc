## Functionality Introduction

Commit or rollback a transaction.

## Function Declaration

```c
SQLRETURN SQLEndTran(  
     SQLSMALLINT   HandleType,  
     SQLHANDLE     Handle,  
     SQLSMALLINT   CompletionType);
```

## Parameter Description

|Parameter Name |Description |
| ------------------- | --------------------------------------------- |
| HandleType (IN)      | Handle type identifier.                             |
| Handle (IN)          | Handle of the type indicated by HandleType, indicating the scope of the transaction. |
| CompletionType (IN)  | SQL_COMMIT or SQL_ROLLBACK.                        |

Support status for the HandleType parameter:

|HandleType |Description |Support Status |
| -------------- | -------------------------------------- | -------- |
| SQL_HANDLE_DBC  | Commit or rollback the transaction for the current connection. | Supported       |
| SQL_HANDLE_ENV  | Commit or rollback transactions for all connections under the current environment handle. | Supported       |