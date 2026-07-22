## Functionality Overview

The OCISessionPoolCreate function is used to create a session pool.

## Function Declaration

```c
sword OCISessionPoolCreate ( OCIEnv           *envhp,
                             OCIError         *errhp, 
                             OCISPool         *spoolhp,
                             OraText         **poolName,
                             ub4              *poolNameLen,
                             const OraText    *connStr,
                             ub4               connStrLen,
                             ub4               sessMin, 
                             ub4               sessMax, 
                             ub4               sessIncr,
                             OraText          *userid,
                             ub4               useridLen,
                             OraText          *password,
                             ub4               passwordLen,
                             ub4               mode );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------------------------------------------ |
| envhp(IN)         | Environment handle.                                        |
| errhp(IN/OUT)     | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to retrieve diagnostic information. |
| spoolhp(IN/OUT)   | Session pool handle.                                      |
| poolName(OUT)     | Session pool name.                                       |
| poolNameLen(OUT)  | Length of the session pool name.                         |
| connStr(IN)       | Connection string for the database server.               |
| connStrLen(IN)    | Length of the connection string for the database server.  |
| sessMin(IN)       | Minimum number of sessions in the session pool, reserved parameter. |
| sessMax(IN)       | Maximum number of sessions in the session pool, reserved parameter. |
| sessIncr(IN)      | Dynamic expansion increment for the session pool, reserved parameter. |
| userid(IN)        | Username.                                              |
| useridLen(IN)     | Length of the username.                                   |
| password(IN)      | Password.                                              |
| passwordLen(IN)   | Length of the password.                                   |
| mode(IN)          | Mode, reserved parameter.                                 |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|-------------|------------------------------------|
| poolName         | Non-NULL                                |
| poolNameLen      | Non-0                                   |
| connStr          | Non-NULL                                |
| connStrLen       | Non-0                                   |
| userid           | Non-NULL                                |
| useridLen        | Non-0                                   |
| password         | Non-NULL                                |
| passwordLen      | Non-0                                   |