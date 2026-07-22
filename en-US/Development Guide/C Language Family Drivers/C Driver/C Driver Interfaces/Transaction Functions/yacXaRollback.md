## Functionality Introduction

The yacXaRollback function is used to rollback an XA transaction. It returns YAC_SUCCESS to indicate a successful rollback and YAC_ERROR to indicate a failed rollback.

## Function Declaration

```c
yacResult yacXaRollback(yacHandle hConn,
                       YacXid* xid,
                       YacUint32 flags);
```

## Parameter Description

|  Parameter Name | Description |
| ---------- | -------------- |
| hConn (IN) | Connection information handle. |
| xid (IN)   | Global unique identifier for XA transaction. |
| flags (IN) | Transaction flags:<br />- YAC_TMNOFLAGS (0): Normal rollback of transaction branch<br />- YAC_TMJOIN (0x00200000): Rollback a joined transaction branch |