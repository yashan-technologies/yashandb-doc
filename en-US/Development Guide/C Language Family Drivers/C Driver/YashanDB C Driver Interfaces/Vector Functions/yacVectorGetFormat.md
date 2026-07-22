## Functionality Overview

The yacVectorGetFormat function is used to get the value type of the vector in a yacVector object. Returning YAC_SUCCESS indicates that the retrieval was successful, while returning YAC_ERROR indicates that the retrieval failed.

## Function Declaration

```c
YacResult yacVectorGetFormat(YacVector* vector,
                             YacVectorFormat* format);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN/OUT)   | The source vector descriptor used to get the value type. |
| format (OUT)      | The value type of the vector. |
