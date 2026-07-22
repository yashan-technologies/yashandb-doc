Can an OS user log in to YashanDB through the OS authentication method, provided the following prerequisites are met:

- **The OS authentication functionality is enabled**

- **The OS authentication has been granted for the user**

- **Using UDS local connection**

    Login requests using the OS authentication method must be initiated from a UDS local connection on the database server. This requires the user to first have the identity of an OS user on the server and be able to successfully log in to the server.

- **Identity must be specified when making the request**

    The login request command using the OS authentication method must include the field `as sysdba` to specify the identity.

Under the condition that all prerequisites are satisfied, if you need to log in to YashanDB using the OS authentication method, the YashanDB user password can be omitted in the login request command (but not the `/` symbol), or any password can be used. Example commands are as follows:

```shell
$ yasql / as sysdba
$ yasql sales/******** as sysdba
```

The YashanDB server's response to this format of login request is as follows:

- The server ignores the username and password in the request command, such as sales/sales in the examples above, and does not validate their correctness.

- The server processes the login request using UDS local connection mode.
- The server checks if the OS user making the login request belongs to the YASDBA user group. If so, the request is granted, and the user logs in to the database as sys; otherwise, the request is denied.

> **Note**:
>
> If using OS authentication, manually changing the `/etc/passwd` file may cause failures; please try logging in again.