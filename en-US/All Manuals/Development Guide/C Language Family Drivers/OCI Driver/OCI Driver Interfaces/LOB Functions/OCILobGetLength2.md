## Functionality Introduction

The OCILobGetChunkSize function is used to obtain the length of a LOB and is applicable only to LOBs larger than 4GB.

## Function Declaration

```c
sword OCILobGetLength2 ( OCISvcCtx      *svchp,
                         OCIError       *errhp,
                         OCILobLocator  *locp,
                         oraub8         *lenp );
```

## Parameter Description

|Parameter Name |Description |
| ----------- |--------------------------------------------------------------------------------|
| svchp (IN)      | Service context handle.                                                              |
| errhp (IN/OUT)  | An error handle that can be used to obtain diagnostic information when an error occurs. |
| locp (IN)       | An internal LOB locator that uniquely references the LOB.                             |
| lenp (OUT)      | If the LOB is not NULL, lenp returns the length of the LOB:<br/>If it points to a BLOB, lenp returns the byte length.<br/>If it points to a CLOB or NCLOB, lenp returns the character length. |