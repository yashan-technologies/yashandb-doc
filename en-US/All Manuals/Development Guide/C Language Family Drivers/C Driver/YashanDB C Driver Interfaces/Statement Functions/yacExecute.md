## Functionality Introduction

The yacExecute function is used to initiate a request to execute the pre-compiled SQL from the yacPrepare function on the client side. It returns YAC_SUCCESS to indicate successful execution and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacExecute(yacHandle hStmt);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------- |
| hStmt (IN/OUT)  | SQL information handle.      |