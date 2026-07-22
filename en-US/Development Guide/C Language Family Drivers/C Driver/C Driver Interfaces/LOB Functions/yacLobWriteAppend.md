## Functionality Introduction

The `yacLobWriteAppend` function is used to append data to the end of a LOB in the LOB protocol. It returns `YAC_SUCCESS` to indicate a successful append and `YAC_ERROR` to indicate a failure.

## Function Declaration

```c
yacResult yacLobWriteAppend ( yacHandle      hConn,
                              yacLobLocator* locator,
                              YacUint64*     byteSize,
                              YacUint64*     charSize,
                              YacUint8*      buf,
                              YacUint64      bufLen);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)     | A handle that records various information related to the connection. |
| locator (IN)       | The loblocator in the LOB protocol.                        |
| byteSize (IN/OUT)  | The byte length of the data to be written. <br> For CLOB, it's automatically ignored when `charSize` is not zero, and it returns the actual byte length of the written data. |
| charSize (IN/OUT)  | The character length of the data to be written. <br> For BLOB, it's automatically ignored and returns the actual character length of the written data. |
| buf (IN)           | The data that the user needs to upload.                    |
| bufLen (IN)        | The length of the storage area for the user's uploaded data. |