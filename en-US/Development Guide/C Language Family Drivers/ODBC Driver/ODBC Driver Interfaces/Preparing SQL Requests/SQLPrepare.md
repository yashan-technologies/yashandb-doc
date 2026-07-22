## Functionality Introduction

Prepare an SQL statement for later execution.

## Function Declaration

```c
SQLRETURN SQLPrepare(  
     SQLHSTMT      StatementHandle,  
     SQLCHAR *     StatementText,  
     SQLINTEGER    TextLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | --------------------------------------- |
| StatementHandle (IN) | The statement handle.                     |
| StatementText (IN)   | The SQL statement to be executed.        |
| TextLength (IN)      | The length of *StatementText (in characters). |