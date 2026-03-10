## Functionality Introduction

Execute a statement.

## Function Declaration

```c
SQLRETURN SQLExecDirect(  
     SQLHSTMT     StatementHandle,  
     SQLCHAR *    StatementText,  
     SQLINTEGER   TextLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | --------------------------------------- |
| StatementHandle (IN)  | Statement handle.                         |
| StatementText (IN)    | The SQL statement to be executed.        |
| TextLength (IN)       | Length of *StatementText (in characters). |