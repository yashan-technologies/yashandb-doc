## Functionality Introduction

The yacCommit function is used for the client to initiate a commit request. It returns YAC_SUCCESS to indicate a successful commit and YAC_ERROR to indicate a failed commit.

## Function Declaration

```c
yacResult yacCommit(yacHandle hConn);
```

## Parameter Description

|Parameter Name |Description |
| ---------- | -------------- |
| hConn (IN)     | Connection information handle. |