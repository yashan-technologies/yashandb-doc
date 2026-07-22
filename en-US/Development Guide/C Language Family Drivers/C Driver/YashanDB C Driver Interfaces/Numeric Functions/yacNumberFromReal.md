## Functionality Introduction

The yacNumberFromReal function is used to assign a real number to a YacNumber. It returns YAC_SUCCESS to indicate a successful setting and YAC_ERROR to indicate a failed setting.

## Function Declaration

```c
YacResult yacNumberFromReal(const YacPointer rnum,
                         YacUint32 length,
                         YacNumber* number);
```

## Parameter Description

|Parameter Name |Description |
| -------------- |-------------------|
| rnum (IN)       | A pointer to the real number to be converted.  |
| length (IN)     | The length of the real number (in bytes).      |
| number (IN/OUT) | The YacNumber handle to be modified.            |