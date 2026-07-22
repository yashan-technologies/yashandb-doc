## Functionality Introduction

The yacLobWrite function is used to upload data from buf in the LOB protocol and store it in the server's LOB. It returns YAC_SUCCESS to indicate a successful write, and YAC_ERROR to indicate a write failure.

> **Note**: 
>
> This function has been deprecated. It is recommended to use [yacLobWrite2](../LOB Functions/yacLobWrite2).

## Function Declaration

```c
yacResult yacLobWrite ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     bytes,
                       YacUint8*      buf,
                       YacUint64      bufLen);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------- |
| hConn (IN/OUT)   | A handle that records various information related to the connection. |
| locator (IN)     | The loblocator in the LOB protocol.      |
| bytes (IN)       | Reserved parameter, not used in this interface. |
| buf (IN)         | The data that the user needs to upload.  |
| bufLen (IN)      | The actual length of the user-uploaded data, expressed in bytes. |