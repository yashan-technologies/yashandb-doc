## Functionality Introduction

Assign the source binary string to yacRaw. A return value of YAC_SUCCESS indicates that the assignment was successful, while a return value of YAC_ERROR indicates that the assignment failed.

## Function Declaration

```c
YacResult yacRawAssignBytes(YacHandle hEnv, const YacUint8* raw, YacUint32 rawLen, YacRaw** yacRaw);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ---------------------- |
| hEnv (IN)          | Environment handle.    |
| raw (IN)           | Source binary string.   |
| rawLen (IN)        | Source binary string length. |
| yacRaw (IN/OUT)    | The yacRaw handle to be modified. |

To initialize yacRaw, you must first call yacRawAssignBytes or yacRawResize.