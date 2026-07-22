## Functionality Overview

The OCIDateTimeToText function is used to convert a datetime type into a time string.

## Function Declaration

```c
sword OCIDateTimeToText(void              *hndl, 
                        OCIError          *err, 
                        const OCIDateTime *date,
                        const OraText     *fmt, 
                        ub1               fmt_length, 
                        ub1               fsprec,
                        const OraText     *lang_name, 
                        size_t            lang_length,
                        ub4               *buf_size, 
                        OraText           *buf );
```

## Parameter Description

|Parameter Name |Description |
|-------------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)           | User session handle or environment handle.                                                                          |
| err (IN/OUT)        | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Obtain diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date (IN)           | Pointer to the OCIDateTime descriptor from which the date information is retrieved.                                  |
| fmt (IN)            | Time string format, for example, YYYY-MM-DD HH24:MI:SS.FF.                                                         |
| fmt_length (IN)     | Length of the time string format parameter.                                                                          |
| fsprec (IN)         | Default is 0; the precision of the fractional seconds value is represented by fmt.                                   |
| lang_name (IN)      | Language to support the return of the date, currently only supports EN.                                             |
| lang_length (IN)    | Length of the language parameter for the returned date.                                                             |
| buf_size (IN/OUT)   | Specifies the maximum length of buf and returns the actual length of buf.                                            |
| buf (OUT)           | Converted time string.                                                                                               |