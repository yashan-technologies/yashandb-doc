## Functionality Introduction

The yacXaStart function is used to start an XA transaction. It returns YAC_SUCCESS to indicate a successful start and YAC_ERROR to indicate a failed start.

## Function Declaration

```c
yacResult yacXaStart(yacHandle hConn,
                    YacXid* xid,
                    YacUint32 flags,
                    YacUint32 timeout);
```

## Parameter Description

|  Parameter Name |  Description |
| ---------- | -------------- |
| hConn (IN) | Connection information handle. |
| xid (IN)   | Global unique identifier for XA transaction, a structure containing formatID, gtrid and bqual fields. |
| flags (IN) | Transaction flags:<br />- YAC_TMNOFLAGS (0): Start a new transaction branch<br />- YAC_TMJOIN (0x00200000): Join an existing transaction branch |
| timeout (IN) | Transaction timeout in seconds; 0 means use system default timeout. |