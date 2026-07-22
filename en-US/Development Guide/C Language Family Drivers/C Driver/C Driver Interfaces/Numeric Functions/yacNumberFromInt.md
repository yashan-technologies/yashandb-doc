## Functionality Overview

The `yacNumberFromInt` function is used to assign an integer to a YacNumber. It returns `YAC_SUCCESS` to indicate a successful setting, and `YAC_ERROR` to indicate a failure in setting.

## Function Declaration

```c
YacResult yacNumberFromInt(const YacPointer inum,
                         YacUint32 length,
                         YacUint32 flag,
                         YacNumber* number);
```

## Parameter Description

|Parameter Name |Description |
|-----------------|------------------------------------------------------------|
| inum (IN)       | A pointer to the integer to be converted.                                   |
| length (IN)     | The length of the integer (in bytes).                                      |
| flag (IN)       | The signedness flag for the integer. `YAC_NUMBER_SIGNED` indicates signed, `YAC_NUMBER_UNSIGNED` indicates unsigned. |
| number (IN/OUT) | The YacNumber handle to be modified.                                       |