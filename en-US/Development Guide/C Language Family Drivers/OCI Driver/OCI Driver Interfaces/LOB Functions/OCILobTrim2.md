## Functionality Overview

The OCILobTrim2 function is used to truncate a LOB value to a shorter length.

## Function Declaration

```c
sword OCILobTrim2 ( OCISvcCtx       *svchp,
                    OCIError        *errhp,
                    OCILobLocator   *locp,
                    oraub8           newlen );
```

## Parameter Description

|Parameter Name |Description |
|----------------|------------------------------------------------------------|
| svchp (IN/OUT)      | Service context handle.                                       |
| errhp (IN/OUT)      | An error handle used to get diagnostic information when an error occurs. |
| locp (IN)           | A LOB locator that uniquely references the LOB.              |
| newlen (IN)         | The new length of the LOB value, which must be less than or equal to the current LOB length. For character LOBs, it is the number of characters; for BLOBs, it is the number of bytes in the LOB. |