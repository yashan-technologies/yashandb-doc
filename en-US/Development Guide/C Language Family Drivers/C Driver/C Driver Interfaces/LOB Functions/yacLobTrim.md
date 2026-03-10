## Functionality Introduction

The yacLobTrim function is used to trim LOB data in the LOB protocol. It returns YAC_SUCCESS to indicate a successful trim and YAC_ERROR to indicate a failure.

## Function Declaration

```c
yacResult yacLobTrim ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     newlen);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)   | A handle that records various information related to the connection. |
| locator (IN)     | The loblocator in the LOB protocol.                       |
| newlen (IN)      | The new length to which the LOB will be trimmed.<br>If the loblocator points to CLOB or NCLOB, newlen is in character representation.<br>If the loblocator points to BLOB, newlen is in byte representation. |