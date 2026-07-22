## Functionality Overview

The yacVectorGetDimension function is used to get the dimension of the vector in a yacVector object. Returning YAC_SUCCESS indicates that the retrieval was successful, while returning YAC_ERROR indicates that the retrieval failed.

## Function Declaration

```c
YacResult yacVectorGetDimension(YacVector* vector,
                                YacUint16* dim);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN/OUT)   | The source vector descriptor used to get the dimension. |
| dim (OUT)         | The dimension of the vector. |
