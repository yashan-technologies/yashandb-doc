## Functionality Overview

The yacVectorToArray function is used to convert a yacVector object to an array. Returning YAC_SUCCESS indicates that the conversion was successful, while returning YAC_ERROR indicates that the conversion failed.

## Function Declaration

```c
YacResult yacVectorToArray(YacVector* vector,
                           YacVectorFormat format,
                           YacUint16* dim,
                           YacUint8* array,
                           YacUint32* arrayLen,
                           YacUint32 mode);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN/OUT)   | The source vector descriptor used for conversion. |
| format (IN)       | Specify the value type of the vector, optional values are [YAC_VECTOR_FORMAT_FLOAT32, YAC_VECTOR_FORMAT_FLOAT64]. |
| dim (IN)          | Specify the dimension value of the constructed vector, range (0, 65535]. |
| dim (OUT)         | Return the number of elements in the converted array. |
| array (OUT)       | The starting address that receives the converted array. |
| arrayLen (OUT)    | Return the byte length of the converted array. |
| mode (IN)         | Reserved parameter. |
