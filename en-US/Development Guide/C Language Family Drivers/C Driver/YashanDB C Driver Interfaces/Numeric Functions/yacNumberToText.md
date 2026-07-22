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
| fmt (IN)        | Format string, specifying the conversion format, supporting combinations of the following characters:<br />- Decimal point: `.`, e.g., '99.99'<br />- Dollar sign: `$`, e.g., '$9,999'<br />- Force zero display: `0`, e.g., '00000'<br />- Positioning numbers: `9`, e.g., '9999'<br />- Positioning decimal points: `D`, e.g., '99D99'<br />- Specify leading/trailing as - or +: `S`, e.g., 'S9999'<br />- Thousand separator: `,`, e.g., '9,999'<br />- Positioning thousand separators: `G`, e.g., '9G9'<br />- Trim spaces and extra zeros: `FM`, e.g., 'FM999'<br />- Return space when integer is zero: `B`, e.g., 'B99'<br />- Positioning currency symbol: `C`, e.g., 'C99'<br />- Scientific notation: `EEEE`, e.g., '9EEEE'<br />- Display currency symbol: `L`, e.g., 'L99'<br />- Negative sign at end: `MI`, e.g., '9MI'<br />- Negative values in <>: `PR`, e.g., '9PR'<br />- Hexadecimal value: `X`, e.g., 'FFF'                                      |
| fmtLength (IN)  | Length of the format string (in characters).       |
| nlsParam (IN)   | Globalization support format specification string, used to specify localized characters for number formatting. If it is a NULL string (`(YacChar*)0`), the default parameters of the session are used.<br />Format: 'NLS_NUMERIC_CHARACTERS=''dg''', where d is the decimal separator character and g is the group separator character. For example:<br />- 'NLS_NUMERIC_CHARACTERS=''.,''': period as decimal separator, comma as group separator<br />- 'NLS_NUMERIC_CHARACTERS='''',.''': comma as decimal separator, period as group separator |
| nlsParamLength (IN) | Length of the globalization support format specification string (in characters); if the value is 0, the session's default parameters will be used. |
| str (OUT)      | Output string cache.                                |
| bufLength (IN) | Length of the string cache.                         |
| length (OUT)   | Used to get the actual length of the string; can be NULL if the actual length is not needed. |