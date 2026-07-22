## Functionality Introduction

OCILogon2 is an extended version of OCILogon function, used to obtain a user session that is already connected. The main difference from OCILogon is the addition of the mode parameter, which supports specifying the operation mode.

## Function Declaration

```c
sword OCILogon2 ( OCIEnv          *envhp,
                  OCIError        *errhp,
                  OCISvcCtx      **svchp,
                  const OraText   *username,
                  ub4              uname_len,
                  const OraText   *password,
                  ub4              passwd_len,
                  const OraText   *dbname,
                  ub4              dbname_len );
                  ub4              mode );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------- |
| envhp (IN)      | Environment handle.<br />Its encoding is set by mode, which is inherited from the statement handle derived from envhpp. |
| errhp (IN/OUT)  | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| svchp (IN/OUT)  | Service context handle.                               |
| username (IN)   | The username for login.                              |
| uname_len (IN)  | Length of the username.                              |
| password (IN)   | The password for login.                              |
| passwd_len (IN) | Length of the password.                              |
| dbname (IN)     | Specifies the database server to use.<br />This parameter points to the specified connection string or service point string. |
| dbname_len (IN) | Length of dbname.                                   |
| mode (IN)       | Specifies the operation mode.                         |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ---- | ----------- |
| mode | OCI_DEFAULT |
