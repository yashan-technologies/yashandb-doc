## Functionality Introduction

The OCIDateTimeFromText function is used to parse a date-time string and return the corresponding date value.

## Function Declaration

```c
sword OCIDateTimeFromText( void          *hndl, 
                           OCIError      *err, 
                           const OraText *date_str,
                           size_t        dstr_length, 
                           const OraText *fmt, 
                           ub1           fmt_length,
                           const OraText *lang_name, 
                           size_t        lang_length, 
                           OCIDateTime   *date );
```

## Parameter Description

|Parameter Name |Description |
|------------------|-----------------------------------------------------------------------------------------------|
| hndl (IN)        | User session handle or environment handle.                                                        |
| err (IN/OUT)     | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date_str (IN)    | Date-time string.                                                                                 |
| dstr_length (IN) | Length of the date-time string parameter.                                                          |
| fmt (IN)         | Date-time string format, such as YYYY-MM-DD HH24:MI:SS.FF.                                       |
| fmt_length (IN)  | Length of the date-time string format parameter.                                                  |
| lang_name (IN)   | Language for the returned date, only supports EN.                                                 |
| lang_length (IN) | Length of the language parameter for the returned date.                                          |
| date (OUT)       | Parsed date-time.                                                                                 |