## Functionality Overview

OCILobTrim2 is an extended version of OCILobTrim function, used to truncate a LOB value to a shorter length. The main difference from OCILobTrim is the use of oraub8 type for the newlen parameter instead of ub4 type, supporting LOB truncate operations larger than 4GB.

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