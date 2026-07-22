## Functionality Introduction

The OCIDateTimeCheck function is used to check if a given date is valid.

## Function Declaration

```c
sword OCIDateTimeCheck( void              *hndl, 
                        OCIError          *err, 
                        const OCIDateTime *date, 
                        ub4               *valid );
```

## Parameter Description

|Parameter Name |Description |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)          | User session handle or environment handle.                                                     |
| err (IN/OUT)       | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Get diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date (IN)          | Pointer to the OCIDateTime descriptor from which date information is retrieved.                 |
| valid (OUT)        | Returns 0 if the date is valid; otherwise, returns the logical OR combination of all error bits specified. |

Error Bit Parameters:

|Macro Name |Bit Value |Error Information |
|------------------------------|--------|---------------------------------------|
| OCI_DT_INVALID_DAY           | 0x1    | Bad day                               |
| OCI_DT_DAY_BELOW_VALID       | 0x2    | Bad day low bit (1=low)               |
| OCI_DT_INVALID_MONTH         | 0x4    | Bad month                             |
| OCI_DT_MONTH_BELOW_VALID     | 0x8    | Bad month low bit (1=low)             |
| OCI_DT_INVALID_YEAR          | 0x10   | Bad year                              |
| OCI_DT_YEAR_BELOW_VALID      | 0x20   | Bad year low bit (1=low)              |
| OCI_DT_INVALID_HOUR          | 0x40   | Bad hour                              |
| OCI_DT_HOUR_BELOW_VALID      | 0x80   | Bad hour low bit (1=low)              |
| OCI_DT_INVALID_MINUTE        | 0x100  | Bad minute                            |
| OCI_DT_MINUTE_BELOW_VALID    | 0x200  | Bad minute low bit (1=low)            |
| OCI_DT_INVALID_SECOND        | 0x400  | Bad second                            |
| OCI_DT_SECOND_BELOW_VALID    | 0x800  | Bad second low bit (1=low)            |
| OCI_DT_DAY_MISSING_FROM_1582 | 0x1000 | Day is one of those missing from 1582 |
| OCI_DT_YEAR_ZERO             | 0x2000 | Year may not equal zero               |
| OCI_DT_INVALID_TIMEZONE      | 0x4000 | Bad time zone                         |
| OCI_DT_INVALID_FORMAT        | 0x8000 | Bad date format input                 |
