## Functionality Overview

The `yacDSIntervalFromText` function is used to assign a source text string to a `yacDSInterval`. It returns `YAC_SUCCESS` to indicate a successful setting, and `YAC_ERROR` to indicate a failure in setting.

## Function Declaration

```c
YacResult yacDSIntervalFromText(YacHandle hEnv,
                                YacDSInterval* dsInterval,
                                const YacChar* str,
                                YacUint32 strLen);
```

## Parameter Description

|Parameter Name |Description |
|----------------------|-------------------|
| hEnv (IN)             | Environment handle.         |
| dsInterval (IN/OUT)   | The `YacDSInterval` handle to be modified. |
| str (IN)              | Source string.              |
| strLen (IN)           | Length of the source string (in bytes). |