## Functionality Overview

The OCIDateTimeConstruct function is used to construct a date-time descriptor.

## Function Declaration

```c
sword OCIDateTimeConstruct ( void          *hndl,
                             OCIError      *err,
                             OCIDateTime   *datetime,
                             sb2            year,
                             ub1            month,
                             ub1            day,
                             ub1            hour,
                             ub1            min,
                             ub1            sec,
                             ub4            fsec,
                             OraText       *timezone,
                             size_t         timezone_length );
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------------------------------ |
| hndl (IN)             | User session handle or environment handle.                  |
| err (IN/OUT)          | OCI error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| datetime (IN)         | Pointer to the OCIDateTime descriptor.                      |
| year (IN)             | Year value.                                                |
| month (IN)            | Month value.                                               |
| day (IN)              | Day value.                                                 |
| hour (IN)             | Hour value.                                                |
| min (IN)              | Minute value.                                              |
| sec (IN)              | Second value.                                              |
| fsec (IN)             | Fractional second value.                                   |
| timezone (IN)         | Time zone string.                                         |
| timezone_length (IN)  | Length of the time zone string.                           |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| --------------- | ------ |
| timezone        | NULL   |
| timezone_length | 0      |
