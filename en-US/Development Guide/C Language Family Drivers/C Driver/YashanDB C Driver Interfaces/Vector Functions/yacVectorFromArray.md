## Functionality Overview

The yacVectorFromArray function is used to construct a yacVector object from an array. Returning YAC_SUCCESS indicates that the construction was successful, while returning YAC_ERROR indicates that the construction failed.

## Function Declaration

```c
YacResult yacVectorFromArray(YacVector* vector,
                             YacVectorFormat format,
                             YacUint16 dim,
                             YacUint8* array,
                             YacUint32 arrayLen,
                             YacUint32 mode);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN/OUT)   | The descriptor that receives the constructed vector. |
| format (IN)       | Specify the value type of the constructed vector, optional values are [YAC_VECTOR_FORMAT_FLOAT32, YAC_VECTOR_FORMAT_FLOAT64]. |
| dim (IN)          | Specify the dimension value of the constructed vector, range (0, 65535]. |
| array (IN)        | The starting address of the original binary floating-point array used to construct the vector. |
| arrayLen (IN)     | The byte length of the original binary floating-point array used to construct the vector. |
| mode (IN)         | Reserved parameter. |
