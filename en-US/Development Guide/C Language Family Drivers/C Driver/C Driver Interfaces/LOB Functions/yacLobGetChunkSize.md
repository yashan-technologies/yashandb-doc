## Functionality Introduction

The yacLobGetChunkSize function is used to retrieve the chunkSize in the LOB protocol. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacLobGetChunkSize ( yacHandle      hConn,
                               yacLobLocator* locator,
                               yacUint16*     chunkSize);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------ |
| hConn (IN/OUT)    | A handle that records various information related to the connection. |
| locator (IN)      | The loblocator in the LOB protocol.        |
| chunkSize (OUT)   | Returns the retrieved chunkSize, which is in bytes. |