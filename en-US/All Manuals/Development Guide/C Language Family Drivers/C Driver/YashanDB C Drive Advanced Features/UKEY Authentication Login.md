UKEY authentication login (UKEY Auth) is a new client authentication method that leverages the signature and encryption functionalities of UKEY hardware to achieve a more secure authentication login.

UKEY authentication login cannot replace the user password. The user password is still required when using UKEY authentication login.

> **Note**:
>
> YashanDB does not manage the password of the UKEY device, but requires that:
>
>- The device name of UKEY must match the user role (DBA, SECURITY_ADMIN, or AUDIT_ADMIN, one of the three).
>- The app name and container name of UKEY must both be YashanDB.

## Configuration Parameters

Parameter names and values are case-insensitive. The information for each parameter is shown in the table below.

|Parameter Name |Legal Value |Description |
|--------------|---------------------|------------------------------|
| UKEY_NAME | * DBA: If the user role logging in is DBA, then the UKEY device name must be DBA<br />* SECURITY_ADMIN: If the user role logging in is SECURITY_ADMIN, then the UKEY device name must be SECURITY_ADMIN<br />* AUDIT_ADMIN: If the user role logging in is AUDIT_ADMIN, then the UKEY device name must be AUDIT_ADMIN | This parameter indicates the name of the UKEY device to be authenticated, which must correspond to the user role name. |
| UKEY_PIN | Password value less than 64 bits | This parameter specifies the user password of the UKEY. |

## Configuring UKEY Authentication Login

The UKEY authentication login functionality is turned off by default. To use it, please contact our technical support for assistance in configuring the environment and enabling this functionality. Once enabled, additional configurations must be completed to use UKEY authentication login. The configuration methods are as follows:

### Method One

1. Navigate to the $YASDB_HOME/client path and add the configuration in the yasc_service.ini file. The configuration example is as follows:
   ```C
   REMOTE = 192.168.1.2:1688??UKEY_NAME=DBA&UKEY_PIN=Yashan123
   ```

2. Connect using the REMOTE data source:
   ```c
   const YacChar* gSrvStr = "REMOTE";
   YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
   ```

### Method Two

Configure directly in the connection URL:
```c
const YacChar* gSrvStr = "192.168.1.2:1688??UKEY_NAME=DBA&UKEY_PIN=Yashan123";
YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
```
