## Functionality Introduction

The yacLobRead function is used to read data from the server's LOB in the LOB protocol and store it in buf. It returns YAC_SUCCESS to indicate a successful read and YAC_ERROR to indicate a failed read.

> **Note**: 
>
> This function has been deprecated. It is recommended to use [yacLobRead2](../LOB Functions/yacLobRead2).

## Function Declaration

```c
yacResult yacLobRead ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     bytes,
                       YacUint8*      buf,
                       YacUint64      bufLen);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)  | A handle that records various information related to the connection. |
| locator (IN)    | The loblocator in the LOB protocol.                         |
| bytes (IN/OUT)  | Input: Total length of data to be written.<br/>Output: Actual length of data written.<br/>Both are measured in bytes. |
| buf (OUT)       | Stores the data that has been read.                         |
| bufLen (IN)     | Reserved parameter, not used in this interface yet.        |