## Functionality Overview

The yacVectorToText function is used to convert a yacVector object to text. Returning YAC_SUCCESS indicates that the conversion was successful, while returning YAC_ERROR indicates that the conversion failed.

## Function Declaration

```c
YacResult yacVectorToText(YacVector* vector,
                          YacChar* text,
                          YacUint32* textlen,
                          YacUint32 mode);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| vector (IN)      | The source vector descriptor used for string conversion. |
| text (OUT)       | The starting address of the buffer that receives the converted string. |
| textlen (OUT)    | The actual length of the converted string. |
| mode (IN)        | Reserved parameter. |
