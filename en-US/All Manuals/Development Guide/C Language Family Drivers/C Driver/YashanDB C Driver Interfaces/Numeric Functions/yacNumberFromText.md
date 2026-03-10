## Functionality Overview

The `yacNumberFromText` function is used to assign a source text string to a `YacNumber`. It returns `YAC_SUCCESS` to indicate a successful setting and `YAC_ERROR` to indicate a failure in setting.

## Function Declaration

```c
YacResult yacNumberFromText(const YacChar* str,
                         YacUint32 strLength,
                         const YacChar* fmt,
                         YacUint32 fmtLength,
                         const YacChar* nlsParam,
                         YacUint32 nlsParamLength,
                         YacNumber* number);
```

## Parameter Description

|Parameter Name |Description |
|-----------------|---------------------------------------------------|
| str (IN)            | The source text string.                             |
| strLength (IN)      | The length of the source text string (in characters). |
| fmt (IN)            | The formatted string.                               |
| fmtLength (IN)      | The length of the formatted string (in characters). |
| nlsParam (IN)       | The globalization support format specification string. If it is a NULL string (`(YacChar*)0`), the default parameters of the session are used. |
| nlsParamLength (IN) | The length of the globalization support format specification string (in characters). If the value is 0, the default parameters of the session are used. |
| number (IN/OUT)     | The `YacNumber` handle to be modified.             |