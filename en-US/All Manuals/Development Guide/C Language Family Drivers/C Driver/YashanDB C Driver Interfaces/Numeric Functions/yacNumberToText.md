## Functionality Overview

The `yacNumberToText` function is used to convert a `YacNumber` type to a `String` type. It returns `YAC_SUCCESS` to indicate a successful conversion, or `YAC_ERROR` to indicate a failure. 

## Function Declaration

```c
YacResult yacNumberToText(const YacNumber* number,
                         const YacChar* fmt,
                         YacUint32 fmtLength,
                         const YacChar* nlsParam,
                         YacUint32 nlsParamLength,
                         YacChar* str,
                         YacInt32 bufLength,
                         YacInt32* length);
```

## Parameter Description

|Parameter Name |Description |
| -------------- |---------------------------------------------------|
| number (IN)     | Source `YacNumber` handle.                          |
| fmt (IN)        | Format string.                                      |
| fmtLength (IN)  | Length of the format string (in characters).       |
| nlsParam (IN)   | Globalization support format specification string; if it is a NULL string (`(YacChar*)0`), the session's default parameters will be used. |
| nlsParamLength (IN) | Length of the globalization support format specification string (in characters); if the value is 0, the session's default parameters will be used. |
| str (OUT)      | Output string cache.                                |
| bufLength (IN) | Length of the string cache.                         |
| length (OUT)   | Used to get the actual length of the string; can be NULL if the actual length is not needed. |