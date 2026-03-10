```ebnf+diagram
SYS_CONTEXT::= SYS_CONTEXT "(" namespace "," parameter ["," length] ")"
```

The SYS_CONTEXT function returns the value of the parameter associated with the namespace at the current moment.

The return value type of the function is VARCHAR, with a default length of 256 bytes.

**namespace**

This parameter must be the built-in namespace USERENV of YashanDB, case insensitive. When other values are entered, the function returns NULL.

**parameter**

Specifies the parameter, which must be of character type or another type that can be implicitly converted to character type (LOB types support implicit conversion). Case insensitive. The specified parameter must be associated with the namespace; if an illegal parameter is provided, an error will occur.

**length**

Specifies the length of the return value. It must be of numeric type or another type that can be implicitly converted to numeric type and must be within the range of INT type; otherwise, an error will occur.

The system treats the value of length as an integer; for non-integers, the decimal part will be truncated, retaining the integer part.

The legal values for length accepted by this function are in the range [1,4000]; any other integers outside this range will be processed with the default value of 256.

The following table lists the predefined parameters of USERENV:

|parameter |Return Value |
| -------------------------- | ------------------------------------------------------------ |
| AUTHENTICATED_IDENTITY     | The identity used in authentication<br>\* Database user validated by password: returns the database username, same as SCHEMA name<br>\* System user with password file: returns the login name |
| AUTHENTICATION_METHOD      | Authentication method<br>\* Identity validated by password or password file: PASSWORD    |
| CLIENT_INFO                | User session information                                      |
| CLIENT_PROGRAM_NAME        | The name of the session program connected to the database     |
| CURRENT_SCHEMA             | The name of the current default SCHEMA                       |
| CURRENT_SCHEMAID           | The SCHEMAID of the current default SCHEMA                   |
| CURRENT_USER               | The name of the current active database user                  |
| CURRENT_USERID             | The ID of the current active database user                    |
| DB_NAME                    | The name of the current database                               |
| HOST                       | The name of the server connected by the client                |
| INSTANCE                   | The ID of the current instance                                 |
| IP_ADDRESS                 | The IP address of the computer connected by the client        |
| ISDBA                      | Whether the current user has DBA privilege                     |
| LANGUAGE                   | The character set used by the database                        |
| YASDB_HOME                 | The full path name of the YASDB_HOME directory               |
| OS_USER                    | The operating system username of the client process that started the database session |
| SESSION_USER               | The name of the logged-in user                                 |
| SESSION_USERID             | The ID of the logged-in user                                   |
| SID                        | Session ID                                                   |

***Example***

```sql
SELECT SYS_CONTEXT('USERENV', 'SESSION_USER') res FROM DUAL;

RES
----------------------------------------------------------------
SYS

SELECT SYS_CONTEXT('USERENV', 'DB_NAME') res FROM DUAL;

RES
----------------------------------------------------------------
yas
```
