## Functionality Introduction

The yacXaPrepare function is used to prepare an XA transaction. It returns YAC_SUCCESS to indicate a successful prepare and YAC_ERROR to indicate a failed prepare.

## Function Declaration

```c
yacResult yacXaPrepare(yacHandle hConn,
                       YacXid* xid,
                       YacUint32 flags);
```

## Parameter Description

|  Parameter Name | Description |
| ---------- | -------------- |
| hConn (IN) | Connection information handle. |
| xid (IN)   | Global unique identifier for XA transaction. |
| flags (IN) | Reserved parameter, please use YAC_TMNOFLAGS (0). |