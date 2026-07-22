## Functionality Introduction

The yacLobGetLength function is used to obtain the total length of LOB data in the LOB protocol. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacLobGetLength ( yacHandle      hConn,
                            yacLobLocator* locator,
                            yacUint64*     length);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)  | A handle that records various information related to the connection. |
| locator (IN)    | The loblocator in the LOB protocol.                         |
| length (OUT)    | Stores the total length of the data obtained.<br>If the loblocator points to a BLOB, length returns the byte length.<br>If the loblocator points to a CLOB or NCLOB, length returns the character length. |