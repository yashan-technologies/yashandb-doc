## Functionality Introduction

Get the allocation size of yacRaw memory (in bytes). Returns YAC_SUCCESS to indicate successful allocation, and YAC_ERROR to indicate allocation failure.

## Function Declaration

```c
YacResult yacRawAllocSize(YacHandle hEnv, const YacRaw* yacRaw, YacUint32* allocSize);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------ |
| hEnv (IN)      | Environment handle.          |
| yacRaw (IN)    | yacRaw handle.              |
| allocSize (OUT)| Stores the memory size of yacRaw. |