## Functionality Introduction

The yacLobRead2 function is used to specify reading LOB data from a certain position in the LOB protocol. It returns YAC_SUCCESS to indicate a successful read or YAC_ERROR to indicate a failure.

## Function Declaration

```c
yacResult yacLobRead2 ( yacHandle       hConn,
                        yacLobLocator*  locator,
                        YacUint64*      byteSize,
                        YacUint64*      charSize,
                        YacUint64       offset,
                        YacUint8*       buf,
                        YacUint64       bufLen);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ----------------------------------------------------------- |
| hConn (IN/OUT)     | A handle that records various information related to the connection. |
| locator (IN)       | The loblocator in the LOB protocol.                         |
| byteSize (IN/OUT)  | The byte length of the data to be written.<br>For CLOB, this is automatically ignored when charSize is not zero, and the actual byte length of written data is returned. |
| charSize (IN/OUT)  | The character length of the data to be written.<br>This is automatically ignored for BLOB, and the actual character length of written data is returned. |
| offset (IN)        | The offset position where the LOB needs to be inserted.<br>If the loblocator points to CLOB or NCLOB, offset is character-based.<br>If the loblocator points to BLOB, offset is byte-based. |
| buf (OUT)          | The cache where the data to be read is stored.             |
| bufLen (OUT)       | The length of the cache where the data to be read is stored. |