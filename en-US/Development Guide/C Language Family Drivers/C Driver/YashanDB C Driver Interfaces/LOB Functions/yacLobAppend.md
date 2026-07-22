## Functionality Introduction

The yacLobAppend function is used to append one LOB to the end of another LOB in the LOB protocol. It returns YAC_SUCCESS to indicate a successful append, or YAC_ERROR to indicate a failure in the append operation.

## Function Declaration

```c
yacResult yacLobAppend ( yacHandle      hConn,
                         YacLobLocator* dstLob, 
                         YacLobLocator* srcLob);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)    | A handle that records various information related to the connection. |
| dstLob (IN/OUT)   | The loblocator in the LOB protocol, the LOB to which data will be appended. |
| srcLob (IN)       | The loblocator in the LOB protocol, the LOB that contains the data to be appended. Currently, only temporary LOBs are supported. <br> srcLob and dstLob must be of the same type, either all BLOBs or all CLOBs. <br> Currently, only temporary LOBs are supported. |