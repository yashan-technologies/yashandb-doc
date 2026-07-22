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
| fmt (IN)            | Format string, specifying the conversion format, supporting combinations of the following characters:<br />- Decimal point: `.`, for example '99.99'<br />- Dollar sign: `$`, for example '$9,999'<br />- Force zero display: `0`, for example '00000'<br />- Return number at specified position: `9`, for example '9999'<br />- Return decimal point at specified position: `D`, for example '99D99'<br />- Set first or last position to - or +: `S`, for example 'S9999'<br />- Thousand separator: `,`, for example '9,999'<br />- Return thousand separator at specified position: `G`, for example '9G9'<br />- Remove leading/trailing spaces and extra zeros after decimal: `FM`, for example 'FM999' |
| fmtLength (IN)      | The length of the formatted string (in characters). |
| nlsParam (IN)       | Globalization support format specification string, used to specify localized characters for number formatting. If it is a NULL string (`(YacChar*)0`), the default parameters of the session are used.<br />Format: 'NLS_NUMERIC_CHARACTERS=''dg''', where d is the decimal separator character and g is the group separator character. For example:<br />- 'NLS_NUMERIC_CHARACTERS=''.,''': period as decimal separator, comma as group separator<br />- 'NLS_NUMERIC_CHARACTERS='''',.''': comma as decimal separator, period as group separator |
| nlsParamLength (IN) | The length of the globalization support format specification string (in characters). If the value is 0, the default parameters of the session are used. |
| number (IN/OUT)     | The `YacNumber` handle to be modified.             |