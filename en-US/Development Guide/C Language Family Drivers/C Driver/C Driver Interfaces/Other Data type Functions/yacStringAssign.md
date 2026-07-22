## Functionality Introduction

Copies one yacString to another. Returns YAC_SUCCESS to indicate a successful copy, and returns YAC_ERROR to indicate a failure in the copy.

## Function Declaration

```c
YacResult yacStringAssign(YacHandle hEnv, const YacString* yacSrcString, YacString** yacDstString);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ------------------------- |
| hEnv (IN)          | Environment handle.       |
| yacSrcString (IN)  | The yacString handle to be copied. |
| yacDstString (OUT) | The yacString handle to copy to. |