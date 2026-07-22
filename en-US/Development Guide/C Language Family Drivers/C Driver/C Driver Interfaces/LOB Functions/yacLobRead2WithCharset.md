## Functionality Introduction

The yacLobRead2WithCharset function is used to read LOB data from a specified position using a specific character set in the LOB protocol. It returns YAC_SUCCESS to indicate successful reading and YAC_ERROR to indicate reading failure.

## Function Declaration

```c
yacResult yacLobRead2WithCharset ( yacHandle       hConn,
                                   yacLobLocator*  locator,
                                   YacUint64*      byteSize,
                                   YacUint64*      charSize,
                                   YacUint64       offset,
                                   YacUint8*       buf,
                                   YacUint64       bufLen,
                                   YacCharsetCode  charset);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ----------------------------------------------------------- |
| hConn (IN/OUT)     | A handle that records various information related to the connection.       |
| locator (IN)       | The loblocator in the LOB protocol.                        |
| byteSize (IN/OUT)  | The byte length of the data to be written.<br>Automatically ignored if charSize is not zero for CLOB, returning the actual byte length of the written data. |
| charSize (IN/OUT)  | The character length of the data to be written.<br>Automatically ignored for BLOB, returning the actual character length of the written data. |
| offset (IN)        | The offset position where the LOB needs to be inserted.<br>If the loblocator points to CLOB or NCLOB, offset is in character representation.<br>If the loblocator points to BLOB, offset is in byte representation. |
| buf (OUT)          | Stores the cache to be read into.                          |
| bufLen (OUT)       | Stores the length of the cache to be read into.            |
| charset (IN)       | The character set used to read the data. Optional values include [YAC_CHARSET_ASCII, YAC_CHARSET_GBK, YAC_CHARSET_UTF8, YAC_CHARSET_ISO88591, YAC_CHARSET_UTF16, YAC_CHARSET_GB18030]. |