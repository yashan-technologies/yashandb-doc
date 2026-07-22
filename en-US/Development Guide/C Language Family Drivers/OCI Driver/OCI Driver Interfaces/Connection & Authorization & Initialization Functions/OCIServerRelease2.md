## Functionality Overview

OCIServerRelease2 is an extended version used to obtain the corresponding Oracle database version name and version number. It currently fixedly returns the version number Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production. The main difference from OCIServerRelease is the extended return value type.

## Function Declaration

```c
sword OCIServerRelease2( void         *hndlp, 
                         OCIError     *errhp, 
                         OraText      *bufp,
                         ub4           bufsz,
                         ub1           hndltype, 
                         ub4          *versionp,
                         ub4           mode);
```

## Parameter Description

|Parameter Name |Description |
|-------------------| ------------------------------------------------------------ |
| hndlp (IN)           | Handle to the service context.                             |
| errhp (IN/OUT)      | An error handle.<br />On error, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| bufp (IN/OUT)       | Cache that returns version information.                    |
| bufsz (IN)          | Length of the cache (in bytes).                            |
| hndltype (IN)       | Handle type of hndlp, reserved parameter.                  |
| versionp (IN/OUT)   | Version number.                                           |
| mode (IN)           | Mode, reserved parameter.                                   |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|----------| ------------------------------------------------------------ |
| versionp | 19.0.0.0.0 |
| bufp     | Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production |
