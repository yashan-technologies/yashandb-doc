## Functionality Introduction

Adjusts the memory size of the given yacRaw. Returns YAC_SUCCESS to indicate successful adjustment, or YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacRawResize(YacHandle hEnv, YacUint32 newSize, YacRaw** yacRaw);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | -------------------- |
| hEnv (IN)          | Environment handle.   |
| newSize (IN)       | Memory size to adjust to. |
| yacRaw (IN/OUT)    | yacRaw handle.       |

To initialize yacRaw, yacRawAssignBytes or yacRawResize must be called first.

To release, yacRawResize must be called with newSize set to 0.