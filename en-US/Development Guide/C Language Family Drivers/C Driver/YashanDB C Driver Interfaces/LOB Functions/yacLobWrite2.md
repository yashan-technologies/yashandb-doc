## Functionality Overview

The yacLobWrite2 function is used to insert data into a LOB from a specific position within the LOB protocol. It returns YAC_SUCCESS to indicate a successful write, and YAC_ERROR to indicate a failed write.

## Function Declaration

```c
yacResult yacLobWrite2 ( yacHandle      hConn,
                         yacLobLocator* locator,
                         YacUint64*     byteSize, 
                         YacUint64*     charSize,
                         YacUint64      offset,
                         YacUint8*      buf,
                         YacUint64      bufLen);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)      | A handle that records various information related to the connection. |
| locator (IN)        | The lob locator in the LOB protocol.                     |
| byteSize (IN/OUT)   | The byte length of the data to be written.<br>Automatically ignored for CLOB when charSize is non-zero, and returns the actual byte length of the written data. |
| charSize (IN/OUT)   | The character length of the data to be written.<br>Automatically ignored for BLOB, and returns the actual character length of the written data. |
| offset (IN)         | The offset position to insert into the LOB.<br>If the loblocator points to CLOB or NCLOB, offset is expressed in characters.<br>If the loblocator points to BLOB, offset is expressed in bytes. |
| buf (IN)            | The data that the user needs to upload.                  |
| bufLen (IN)        | The length of the storage area for the uploaded data.    |