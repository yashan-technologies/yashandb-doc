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
| fmt (IN)            | Conversion format.                                                                                   |
| fmt_length (IN)     | Length of the conversion format.                                                                      |
| nls_params (IN)     | Globalization support format specifications. If it is a NULL string (""), the session's default parameters are used. |
| nls_p_length (IN)   | Length of globalization support format specifications.                                                |
| number (OUT)        | Converts the given string to OCI NUMBER type.                                                         |