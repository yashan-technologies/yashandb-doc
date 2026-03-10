## Functionality Introduction

The OCIDateSysDate function is used to obtain the current client time.

## Function Declaration

```c
sword OCIDateSysDate( OCIError *err, 
                      OCIDate  *sys_date );
```

## Parameter Description

|Parameter Name |Description |
|------------------|-------------------------------------------------------------------------------------------------|
| err (IN/OUT)     | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Get diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| sys_date (OUT)   | The retrieved time.                                                                                       |