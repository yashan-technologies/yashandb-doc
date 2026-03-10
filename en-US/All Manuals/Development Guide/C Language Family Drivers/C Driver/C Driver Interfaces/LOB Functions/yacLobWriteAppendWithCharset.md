## Functionality Introduction

The yacLobWriteAppendWithCharset function is used to append data to the end of a LOB using a specific character set. It returns YAC_SUCCESS to indicate a successful append and returns YAC_ERROR to indicate a failure.

## Function Declaration

```c
yacResult yacLobWriteAppendWithCharset ( yacHandle      hConn,
                                         yacLobLocator* locator,
                                         YacUint64*     byteSize,
                                         YacUint64*     charSize,
                                         YacUint8*      buf,
                                         YacUint64      bufLen,
                                         YacCharsetCode charset);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)     | A handle that records various connection-related information. |
| locator (IN)       | The loblocator in the LOB protocol.                     |
| byteSize (IN/OUT)  | The byte length of the data to be written.<br>Automatically ignored for CLOB if charSize is not zero, returning the actual byte length of the written data. |
| charSize (IN/OUT)  | The character length of the data to be written.<br>Automatically ignored for BLOB, returning the actual character length of the written data. |
| buf (IN)           | The data that the user needs to upload.                 |
| bufLen (IN)        | The length of the storage area for the uploaded data.    |
| charset (IN)       | The character set used for the uploaded data. Options include [YAC_CHARSET_ASCII, YAC_CHARSET_GBK, YAC_CHARSET_UTF8, YAC_CHARSET_ISO88591, YAC_CHARSET_UTF16, YAC_CHARSET_GB18030]. |