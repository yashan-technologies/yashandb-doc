## Functionality Introduction

The `yacNumberRound` function is used to reset the YacNumber according to the specified precision and scale. It returns `YAC_SUCCESS` to indicate a successful setting, and `YAC_ERROR` to indicate a failure in setting.

## Function Declaration

```c
YacResult yacNumberRound(YacNumber* n,
                         YacInt32 precision,
                         YacInt32 scale);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------- |
| n (IN/OUT)       | Holds the value of type number. |
| precision (IN)   | Precision, the valid range is [1,38]. |
| scale (IN)       | Number of decimal places, the valid range is [-84,127]. |