## Functionality Introduction

The OCIPasswordChange function is used to change a user's password.

## Function Declaration

```c
sword OCIPasswordChange ( OCISvcCtx     *svchp,
                          OCIError      *errhp,
                          const OraText *user_name,
                          ub4            usernm_len,
                          const OraText *opasswd,
                          ub4            opasswd_len,
                          const OraText *npasswd,
                          sb4            npasswd_len,
                          ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| ----------- | -------------------------------------- |
| svchp(IN)      | Handle to the initialized server context.     |
| errhp(IN)      | Error handle, can obtain diagnostic information when an error occurs. |
| user_name      | Username.                                     |
| usernm_len     | Length of the username.                       |
| opasswd        | Old password.                                 |
| opasswd_len    | Length of the old password.                   |
| npasswd        | New password.                                 |
| npasswd_len    | Length of the new password.                   |
| mode(IN)       | Mode, reserved parameter.                     |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|-------------|---------------------------|
| user_name      | Not NULL                   |
| usernm_len     | Not 0                      |
| opasswd        | Not NULL                   |
| opasswd_len    | Not 0                      |
| npasswd        | Not NULL                   |
| npasswd_len    | Not 0                      |
| mode        | OCI_AUTH<br />OCI_DEFAULT |
