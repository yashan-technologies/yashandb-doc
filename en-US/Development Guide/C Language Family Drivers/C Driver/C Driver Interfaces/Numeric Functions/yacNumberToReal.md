## Functionality Introduction

The yacNumberToReal function is used to retrieve the real number stored in YacNumber. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacNumberToReal(const YacNumber* number,
                         YacUint32 length,
                         YacPointer rsl);
```

## Parameter Description

|Parameter Name |Description |
| ------------- |---------------|
| number (IN)    | Source YacNumber handle.      |
| length (IN)    | Length of the real number (in bytes). |
| rsl (IN/OUT)   | Pointer to store the real number. |