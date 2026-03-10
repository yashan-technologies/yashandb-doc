## Functionality Overview

The OCINumberToText function is used to convert an OCI NUMBER to a string type.

## Function Declaration

```c
sword OCINumberToText ( OCIError             *err,
                        const OCINumber      *number, 
                        const OraText        *fmt, 
                        ub4                  fmt_length,
                        const OraText        *nls_params, 
                        ub4                  nls_p_length,
                        ub4                  *buf_size, 
                        OraText              *buf );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| number (IN)       | The OCI NUMBER to be converted.                                                                                        |
| fmt (IN)          | The conversion format.                                                                                                |
| fmt_length (IN)   | The length of the conversion format.                                                                                   |
| nls_params (IN)   | Globalization support format specifications. If it is a NULL string (""), the session's default parameters are used. |
| nls_p_length (IN) | The length of the globalization support format specifications.                                                           |
| buf_size (IN)     | Cache size.                                                                                                           |
| buf (OUT)         | The cache for the converted string.                                                                                    |