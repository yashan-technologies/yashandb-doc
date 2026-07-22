```ebnf
SYS_CONTEXT = SYS_CONTEXT "(" namespace "," parameter ["," length] ")".
```

The SYS_CONTEXT function is used to obtain the value of a specified parameter (`parameter`) associated with the context namespace (`namespace`) at the current moment, and returns a VARCHAR data with a length of `length`.

**namespace**

Specify the target namespace, without case-sensitivity.

Currently, it must be specified as the built-in namespace `USERENV` of YashanDB; otherwise, the function returns NULL.

**parameter**

Specifies the name of the target parameter, which are not case - sensitive. They must be of character type or other types that can be converted to character type (LOB types support implicit conversion).

The specified parameter must be associated with the target namespace; otherwise, an error will be reported.

The parameters predefined by the built-in namespace `USERENV` are shown in the following table:

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
| DB_UNIQUE_NAME | The unique name of the current database, composed of the database creation name and the node ID. For example, `yashandb1-1:1` |
| HOST                       | The name of the server connected by the client                |
| INSTANCE                   | The ID of the current instance                                 |
| INSTANCE_NAME | The name of the current instance |
| IP_ADDRESS                 | The IP address of the computer connected by the client        |
| ISDBA                      | Whether the current user has DBA privilege                     |
| LANGUAGE                   | The character set used by the database                        |
| LANG                   | Abbreviation of the parameter `LANGUAGE`, with the same meaning |
| NLS_DATE_FORMAT         | The date format of the current session |
| OS_USER                    | The operating system username of the client process that started the database session |
| SESSION_USER               | The name of the logged-in user                                 |
| SESSION_USERID             | The ID of the logged-in user                                   |
| SESSIONID | The auditing session ID of the current session, same as the `AUDSID` field in the V$SESSION view |
| SID                        | Session ID                                                   |
| YASDB_HOME                 | The full path name of the YASDB_HOME directory               |
| CURRENT_EDITION_ID | Only for compatibility, its value is always empty |
| SESSION_EDITION_ID | Only for compatibility, its value is always empty |

**length**

Specifies the length of the return value, with the unit being bytes. It must be of a numeric type or other types that can be implicitly converted to a numeric type. This can be omitted, and the default value is 256.

- The value range of `length` is the same as that of the INT type (an error will be reported if it exceeds this range). However, the legal length range of the return value of this function is [1, 4000]. If the value of length is not within this range, it will be treated as the default value of 256.

- The value of `length` is uniformly treated as an integer. If it is specified as a decimal number, the decimal part will be directly truncated to get an integer.


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

SELECT SYS_CONTEXT('USERENV', 'DB_UNIQUE_NAME') res FROM DUAL;

RES
----------------------------------------------------------------
yashandb1-1:1
```
