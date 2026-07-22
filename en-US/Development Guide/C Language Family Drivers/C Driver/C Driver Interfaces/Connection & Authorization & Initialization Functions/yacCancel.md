## Functionality Introduction

The yacCancel function is used to initiate an asynchronous request from the client to cancel the currently processing logic on the server. It returns YAC_SUCCESS to indicate a successful cancellation and YAC_ERROR to indicate a failed cancellation.

## Function Declaration

```c
YacResult yacCancel(YacHandle hConn);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------- |
| hConn (IN/OUT) | Connection information handle. |