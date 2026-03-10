```ebnf+diagram
SYS_CONTEXT::= SYS_CONTEXT "(" namespace "," parameter ["," length] ")"
```

SYS_CONTEXT函数返回当前时刻与namespace关联的parameter值。

函数的返回值类型为VARCHAR，默认长度为256字节。

**namespace**

该参数须为YashanDB内置的命名空间USERENV，不区分大小写，输入其他值时函数返回NULL。

**parameter**

指定参数，须为字符型或可转换为字符型的其他类型（LOB类型支持隐式转换）。大小写不敏感。指定的parameter须与namespace相关联，若为非法parameter，将报错。

**length**

指定返回值的长度。须为数值型或可隐式转换为数值型的其他类型，且必须处于INT类型的值域范围内，否则报错。

系统对length的值按整数处理，对于非整数将进行小数部分截断，保留整数部分。

本函数接受的length合法值在[1,4000]区间，对于不在此范围的其他整数将按默认值256处理。

下表列示USERENV预定义的parameter：

|  parameter| 返回值|
| -------------------------- | ------------------------------------------------------------ |
| AUTHENTICATED_IDENTITY     | 身份验证中使用的标识<br>\* 经过密码验证的数据库用户：返回数据库用户名，与SCHEMA名称相同<br>\* 用密码文件的系统用户：返回登录名 |
| AUTHENTICATION_METHOD      | 身份验证方法<br>\* 经过密码或密码文件验证的身份：PASSWORD    |
| CLIENT_INFO                | 用户会话信息                                                 |
| CLIENT_PROGRAM_NAME        | 与数据库连接的会话程序的名称                                 |
| CURRENT_SCHEMA | 当前活动的默认SCHEMA名称                                     |
| CURRENT_SCHEMAID           | 当前活动的默认SCHEMAID                                       |
| CURRENT_USER               | 当前活动的数据库用户名称                                     |
| CURRENT_USERID             | 当前活动的数据库用户ID                                       |
| DB_NAME                    | 当前数据库名称                                               |
| HOST                       | 客户端连接的服务器名称                                         |
| INSTANCE                   | 当前实例的ID                                                 |
| IP_ADDRESS                 | 客户端连接的计算机IP地址                                     |
| ISDBA                      | 当前用户是否具有DBA权限                                      |
| LANGUAGE                   | 数据库使用的字符集                                           |
| YASDB_HOME                 | YASDB_HOME主目录的完整路径名                                 |
| OS_USER                    | 启动数据库会话的客户端进程的操作系统用户名                   |
| SESSION_USER               | 登录用户的名称                                               |
| SESSION_USERID             | 登录用户的ID                                                 |
| SID                        | 会话ID                                                       |

示例

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
