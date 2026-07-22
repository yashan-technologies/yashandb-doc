## Functionality Introduction

The yacXaForget function is used to discard recovery information related to an XA transaction. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacXaForget(yacHandle hConn,
                     YacXid* xid,
                     YacUint32 flags);
```

## Parameter Description

|  Parameter Name | Description |
| ---------- | -------------- |
| hConn (IN) | Connection information handle. |
| xid (IN)   | Global unique identifier for XA transaction. |
| flags (IN) | Transaction flags:<br />- YAC_TMNOFLAGS (0): Normal discard of recovery information<br />- YAC_TMJOIN (0x00200000): Discard recovery information for a joined transaction branch |

> **Note**:
> 
> If the transaction has already been rolled back, calling Forget will return YAC_ERROR.