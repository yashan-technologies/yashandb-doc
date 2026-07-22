## Functionality Introduction

Get the allocation size of the yacString in memory (in bytes). Returns YAC_SUCCESS to indicate success in allocation, or YAC_ERROR to indicate failure in allocation.

## Function Declaration

```c
YacResult yacStringAllocSize(YacHandle hEnv, const YacString* yacString, YacUint32* allocSize);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------- |
| hEnv (IN)       | Environment handle.               |
| yacString (IN)  | yacString handle.                 |
| allocSize (OUT) | Stores the memory size of yacString. |