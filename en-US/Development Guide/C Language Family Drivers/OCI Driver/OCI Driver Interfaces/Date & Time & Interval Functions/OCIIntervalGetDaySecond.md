## Functionality Introduction

The OCIIntervalGetDaySecond function is used to retrieve the day, hour, minute, and second values in a time interval.

## Function Declaration

```c
sword OCIIntervalGetDaySecond (void               *hndl, 
                               OCIError           *err, 
                               sb4                *dy, 
                               sb4                *hr,
                               sb4                *mm, 
                               sb4                *ss, 
                               sb4                *fsec, 
                               const OCIInterval  *interval );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------------------ |
| hndl (IN)      | User session handle or environment handle.                 |
| err (IN/OUT)   | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| dy (OUT)       | Number of days.                                           |
| hr (OUT)       | Number of hours.                                         |
| mm (OUT)       | Number of minutes.                                       |
| ss (OUT)       | Number of seconds.                                       |
| fsec (OUT)     | Number of nanoseconds.                                   |
| interval (IN)  | Input interval.                                         |