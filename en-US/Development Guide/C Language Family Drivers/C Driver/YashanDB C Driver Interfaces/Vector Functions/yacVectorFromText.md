## Functionality Overview

The yacVectorFromText function is used to construct a yacVector object from text. Returning YAC_SUCCESS indicates that the construction was successful, while returning YAC_ERROR indicates that the construction failed.

## Function Declaration

```c
YacResult yacVectorFromText(YacVector* vector,
                            YacVectorFormat format,
                            YacUint16 dim,
                            YacChar* text,
                            YacUint32 textlen,
                            YacUint32 mode);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN/OUT)   | The descriptor that receives the constructed vector. |
| format (IN)       | Specify the value type of the constructed vector, optional values are [YAC_VECTOR_FORMAT_FLOAT32, YAC_VECTOR_FORMAT_FLOAT64]. |
| dim (IN)          | Specify the dimension value of the constructed vector, range (0, 65535]. |
| text (IN)         | The starting address of the original text buffer used to construct the vector. |
| textlen (IN)      | The length of the original text used to construct the vector. |
| mode (IN)         | Reserved parameter. |
