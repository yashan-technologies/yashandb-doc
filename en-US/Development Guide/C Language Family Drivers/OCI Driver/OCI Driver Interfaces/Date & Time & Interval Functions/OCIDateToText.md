## Functionality Introduction

The OCIDateToText function is used to convert a date type into a date string.

## Function Declaration

```c
sword OCIDateToText( OCIError      *err, 
                     const OCIDate *date,
                     const oratext *fmt, 
                     ub1           fmt_length,
                     const oratext *lang_name, 
                     ub4           lang_length,
                     ub4           *buf_size, 
                     oratext       *buf    );
```

## Parameter Description

|Parameter Name |Description |
|-------------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)           | User session handle or environment handle.                                                        |
| err (IN/OUT)        | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date (IN)           | A pointer to the OCIDate descriptor from which date information is retrieved.                     |
| fmt (IN)            | Date string format, for example, YYYY-MM-DD.                                                    |
| fmt_length (IN)     | Length of the date string format parameter.                                                       |
| lang_name (IN)      | The language for returning the date, only supports EN.                                           |
| lang_length (IN)    | Length of the language parameter for the returned date.                                          |
| buf_size (IN/OUT)   | Specifies the maximum length of buf and returns the actual length of buf.                        |
| buf (OUT)           | The converted date string.                                                                         |