## Functionality Introduction

The yacLobIsTemporary function is used to determine if a LOB is a temporary LOB in the LOB protocol. It returns YAC_SUCCESS to indicate that the operation was successful, and returns YAC_ERROR to indicate that the operation failed.

> **Note**:
> 
> It is recommended to call the yacLobIsTemporary function to determine if the loblocator points to a temporary LOB after the fetch LOB operation. If it does, call [yacLobFreeTemporary](yacLobFreeTemporary) to release the temporary LOB to prevent the accumulation of temporary LOBs on the server, which can significantly slow down the system.

## Function Declaration

```c
yacResult yacLobIsTemporary ( yacHandle      hConn,
                              yacLobLocator* locator,
                              YacBool*       isTemporary);
```

## Parameter Description

|Parameter Name |Description |
| ------------------- | --------------------------------------------- |
| hConn (IN/OUT)      | A handle that records various information related to the connection. |
| locator (IN)        | The loblocator in the LOB protocol.        |
| isTemporary(IN/OUT) | YAC_TRUE indicates a temporary LOB, while YAC_FALSE indicates it is not a temporary LOB. |