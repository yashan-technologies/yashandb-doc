## Functionality Overview

Copies one yacRaw to another. Returns YAC_SUCCESS to indicate the copy was successful, and returns YAC_ERROR to indicate the copy failed.

## Function Declaration

```c
YacResult yacRawAssign(YacHandle hEnv, const YacRaw* yacSrcRaw, YacRaw** yacDstRaw);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ---------------------- |
| hEnv (IN)       | Environment handle.    |
| yacSrcRaw (IN)  | The yacRaw handle to be copied. |
| yacDstRaw (OUT) | The yacRaw handle to copy to. |