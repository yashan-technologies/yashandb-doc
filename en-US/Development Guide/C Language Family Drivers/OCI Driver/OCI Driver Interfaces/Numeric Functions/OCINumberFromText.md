## Functionality Overview

The OCINumberFromText function is used to convert a string to OCI NUMBER type.

## Function Declaration

```c
sword OCINumberFromText ( OCIError           *err, 
                          const OraText      *str, 
                          ub4                str_length,
                          const OraText      *fmt,
                          ub4                fmt_length, 
                          const OraText      *nls_params, 
                          ub4                nls_p_length, 
                          OCINumber          *number );
```

## Parameter Description

|Parameter Name |Description |
|-----------------|---------------------------------------------------------------------------------------------------|
| err (IN/OUT)        | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />You can obtain diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| str (IN)            | Pointer to the string to be converted.                                                               |
| str_length (IN)     | Length of the string.                                                                                 |
| fmt (IN)            | Format string, specifying the conversion format, supports the following character combinations:<br />- Decimal point: `.`, for example '99.99'<br />- Dollar sign: `$`, for example '$9,999'<br />- Force zero display: `0`, for example '00000'<br />- Return number at specified position: `9`, for example '9999'<br />- Return decimal point at specified position: `D`, for example '99D99'<br />- Set first or last position to - or +: `S`, for example 'S9999'<br />- Thousands separator: `,`, for example '9,999'<br />- Return thousands separator at specified position: `G`, for example '9G9'<br />- Remove leading/trailing spaces and extra zeros after decimal: `FM`, for example 'FM999' |
| fmt_length (IN)     | Length of the conversion format.                                                                      |
| nls_params (IN)     | Globalization support format specification string, used to specify localized characters for number formats. If it is a NULL string ((OraText*)0), the session's default parameters are used.<br />Format: 'NLS_NUMERIC_CHARACTERS=''dg''', where d is the decimal point character and g is the thousands separator. Examples:<br />- 'NLS_NUMERIC_CHARACTERS=''.,''': period as decimal point, comma as thousands separator<br />- 'NLS_NUMERIC_CHARACTERS='',.''': comma as decimal point, period as thousands separator |
| nls_p_length (IN)   | Length of globalization support format specifications.                                                |
| number (OUT)        | Converts the given string to OCI NUMBER type.                                                         |