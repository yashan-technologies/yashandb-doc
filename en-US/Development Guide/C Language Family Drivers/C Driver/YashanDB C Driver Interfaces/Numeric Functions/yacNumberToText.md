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
| fmt (IN)        | Format string, specifying the conversion format, supporting combinations of the following characters:<br />- Decimal point: `.`, for example '99.99'<br />- Dollar sign: `$`, for example '$9,999'<br />- Force zero display: `0`, for example '00000'<br />- Return number at specified position: `9`, for example '9999'<br />- Return decimal point at specified position: `D`, for example '99D99'<br />- Set first or last position to - or +: `S`, for example 'S9999'<br />- Thousand separator: `,`, for example '9,999'<br />- Return thousand separator at specified position: `G`, for example '9G9'<br />- Remove leading/trailing spaces and extra zeros after decimal: `FM`, for example 'FM999' |
| fmtLength (IN)  | Length of the format string (in characters).       |
| nlsParam (IN)   | Globalization support format specification string, used to specify localized characters for number formatting. If it is a NULL string (`(YacChar*)0`), the default parameters of the session are used.<br />Format: 'NLS_NUMERIC_CHARACTERS=''dg''', where d is the decimal separator character and g is the group separator character. For example:<br />- 'NLS_NUMERIC_CHARACTERS=''.,''': period as decimal separator, comma as group separator<br />- 'NLS_NUMERIC_CHARACTERS='''',.''': comma as decimal separator, period as group separator |
| nlsParamLength (IN) | Length of the globalization support format specification string (in characters); if the value is 0, the session's default parameters will be used. |
| str (OUT)      | Output string cache.                                |
| bufLength (IN) | Length of the string cache.                         |
| length (OUT)   | Used to get the actual length of the string; can be NULL if the actual length is not needed. |