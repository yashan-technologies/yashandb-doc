## Functionality Introduction

The yacYMIntervalFromText function is used to assign the source text string to yacYMInterval. It returns YAC_SUCCESS to indicate successful setting and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacYMIntervalFromText(YacHandle hEnv,
                                YacYMInterval* ymInterval,
                                const YacChar* str,
                                YacUint32 strLen);
```

## Parameter Description

|Parameter Name |Description |
| --------------- |-----------------------|
| hEnv (IN)        | Environment handle.          |
| ymInterval (IN/OUT) | The YacYMInterval handle to be modified. |
| str (IN)         | Source string.               |
| strLen (IN)      | Length of the source string (in bytes). |