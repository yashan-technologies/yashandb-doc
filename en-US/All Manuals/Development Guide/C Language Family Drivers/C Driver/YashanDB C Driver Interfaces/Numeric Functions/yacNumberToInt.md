## Functionality Introduction

The yacNumberToInt function is used to retrieve the integer stored in a YacNumber. It returns YAC_SUCCESS to indicate successful retrieval and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacNumberToInt(const YacNumber* number,
                         YacUint32 length,
                         YacUint32 flag,
                         YacPointer rsl);
```

## Parameter Description

|Parameter Name |Description |
| ------------- |------------------------------------------------------------|
| number (IN)      | The source YacNumber handle.                                    |
| length (IN)      | The length of the integer (in bytes).                          |
| flag (IN)        | The signed or unsigned flag for the integer. YAC_NUMBER_SIGNED indicates signed, YAC_NUMBER_UNSIGNED indicates unsigned. |
| rsl (IN/OUT)     | Pointer to store the integer.                                   |