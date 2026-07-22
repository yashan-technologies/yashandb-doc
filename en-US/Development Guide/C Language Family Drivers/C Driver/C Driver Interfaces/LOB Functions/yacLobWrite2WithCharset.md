## Functionality Introduction

The yacLobWrite2WithCharset function is used to insert data into a LOB from a specific location using a specific character set. It returns YAC_SUCCESS to indicate a successful write and YAC_ERROR to indicate a failed write.

## Function Declaration

```c
yacResult yacLobWrite2WithCharset ( yacHandle      hConn,
                                    yacLobLocator* locator,
                                    YacUint64*     byteSize, 
                                    YacUint64*     charSize,
                                    YacUint64      offset,
                                    YacUint8*      buf,
                                    YacUint64      bufLen,
                                    YacCharsetCode charset);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)     | A handle that records various information related to the connection. |
| locator (IN)       | The loblocator in the LOB protocol.                    |
| byteSize (IN/OUT)  | The byte length of the data to be written.<br>For CLOB, automatically ignored when charSize is not zero; returns the actual byte length of the written data. |
| charSize (IN/OUT)  | The character length of the data to be written.<br>Automatically ignored for BLOB; returns the actual character length of the written data. |
| offset (IN)        | The offset position in the LOB where data needs to be inserted.<br>If the loblocator points to CLOB or NCLOB, offset is represented in characters.<br>If the loblocator points to BLOB, offset is represented in bytes. |
| buf (IN)           | The data that the user needs to upload.                |
| bufLen (IN)       | The length of the storage area for uploaded data.      |
| charset (IN)      | The character set used for the data uploaded by the user. Optional values are [YAC_CHARSET_ASCII, YAC_CHARSET_GBK, YAC_CHARSET_UTF8, YAC_CHARSET_ISO88591, YAC_CHARSET_UTF16, YAC_CHARSET_GB18030]. |