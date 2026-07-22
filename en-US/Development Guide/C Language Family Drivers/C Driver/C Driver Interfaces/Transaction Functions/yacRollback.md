## Functionality Introduction

The yacRollback function is used to initiate a rollback request from the client. It returns YAC_SUCCESS to indicate that the rollback was successful, and YAC_ERROR to indicate that the rollback failed.

## Function Declaration

```c
yacResult yacRollback(yacHandle hConn);
```

## Parameter Description

|Parameter Name |Description |
| ---------- | -------------- |
| hConn (IN)     | Connection information handle. |