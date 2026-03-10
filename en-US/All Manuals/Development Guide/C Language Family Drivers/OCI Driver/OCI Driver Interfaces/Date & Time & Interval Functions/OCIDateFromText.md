## Functionality Introduction

The OCIDateFromText function is used to parse date-type strings and the return value date corresponds to the date type of the string.

## Function Declaration

```c
sword OCIDateFromText( OCIError      *err, 
                       const oratext *date_str, 
                       ub4           d_str_length, 
                       const oratext *fmt, 
                       ub1           fmt_length, 
                       const oratext *lang_name, 
                       ub4           lang_length, 
                       OCIDate       *date );
```

## Parameter Description

|Parameter Name |Description |
|-------------------|-----------------------------------------------------------------------------------------------|
| err (IN/OUT)        | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Get diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date_str (IN)      | Time string from which the date information is parsed.                                                            |
| d_str_length (IN)   | Length of the time string parameter.                                                                               |
| fmt (IN)           | Format of the time string, such as YYYY-MM-DD.                                                                    |
| fmt_length (IN)    | Length of the time string format parameter.                                                                        |
| lang_name (IN)     | Language for the returned date, only supports EN.                                                                  |
| lang_length (IN)   | Length of the language parameter for the returned date.                                                            |
| date (OUT)         | The parsed date.                                                                                                   |