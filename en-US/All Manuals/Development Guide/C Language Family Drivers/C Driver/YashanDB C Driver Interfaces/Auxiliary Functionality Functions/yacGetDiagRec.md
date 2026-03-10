## Functionality Introduction

When other functions return YAC_ERROR or YAC_SUCCESS_WITHINFO, diagnostic information can be obtained through the yacGetDiagRec function.

## Function Declaration

```c
YacResult yacGetDiagRec(YacInt32* errCode,
                        YacChar* message,
                        YacInt32 bufLen,
                        YacInt32* incicator,
                        YacChar* sqlState,
                        YacInt32 sqlStateBufLen,
                        YacTextPos* pos);
```

## Parameter Description

|Parameter Name |Description |
| ------------------- | ------------------------------- |
| errCode (IN/OUT)    | YashanDB error code number.       |
| message (IN/OUT)    | Address of the error code message buffer. |
| bufLen (IN)         | Size of the error code message buffer. |
| incicator (IN/OUT)  | Pointer to the actual filled size of the error code message buffer. |
| sqlState (IN/OUT)   | Address of the SQL standard error code buffer. |
| sqlStateBufLen (IN) | Size of the SQL standard error code buffer. |
| pos (IN/OUT)        | Location of the error in terms of row and column. |