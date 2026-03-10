## Functionality Introduction

The OCILogon function is used to obtain a connected user session.

## Function Declaration

```c
sword OCILogon ( OCIEnv          *envhp,
                 OCIError        *errhp,
                 OCISvcCtx       **svchp,
                 CONST OraText   *username,
                 ub4             uname_len,
                 CONST OraText   *password,
                 ub4             passwd_len,
                 CONST OraText   *dbname,
                 ub4             dbname_len );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------- |
| envhp(IN)        | Environment handle.<br />The encoding settings are specified by mode, which are inherited from the statement handle derived from envhpp. |
| errhp (IN/OUT)   | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| svchp(IN/OUT)    | Handle for the service context.                         |
| username(IN)     | Username used for login.                                |
| uname_len(IN)    | Length of the username.                                 |
| password(IN)     | Password used for login.                                |
| passwd_len(IN)   | Length of the password.                                 |
| dbname(IN)       | Specifies the database server to be used.<br />This parameter points to a specified connection string or service point string. |
| dbname_len(IN)   | Length of dbname.                                      |