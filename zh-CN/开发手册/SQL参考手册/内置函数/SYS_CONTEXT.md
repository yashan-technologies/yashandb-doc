```ebnf
SYS_CONTEXT = SYS_CONTEXT "(" namespace "," parameter ["," length] ")".
```

SYS_CONTEXT函数用于获取当前时刻与上下文命名空间namespace相关联的某个参数parameter的值，返回一个长度为length的VARCHAR类型数据。

**namespace**

指定目标命名空间，不区分大小写。

目前该参数必须指定为YashanDB内置的命名空间`USERENV`，否则函数返回NULL。

**parameter**

指定目标参数的名称，不区分大小写，须为字符型或可转换为字符型的其他类型（LOB类型支持隐式转换）。

指定的参数必须与目标命名空间相关联，否则将报错。

内置命名空间USERENV预定义的参数如下表所示：

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
| DB_UNIQUE_NAME | 当前数据库的唯一名称，由数据库建库名称和节点ID组成，例如`yashandb1-1:1` |
| HOST                       | 客户端连接的服务器名称                                         |
| INSTANCE                   | 当前实例的ID                                                 |
| INSTANCE_NAME | 当前实例的名称 |
| IP_ADDRESS                 | 客户端连接的计算机IP地址                                     |
| ISDBA                      | 当前用户是否具有DBA权限                                      |
| LANGUAGE                   | 数据库使用的字符集                                           |
| LANG                   | 参数`LANGUAGE`的缩写，含义与其一致                                           |
| NLS_DATE_FORMAT         | 当前会话的日期格式                                           |
| OS_USER                    | 启动数据库会话的客户端进程的操作系统用户名                   |
| SESSION_USER               | 登录用户的名称                                               |
| SESSION_USERID             | 登录用户的ID                                                 |
| SESSIONID | 当前会话的审计会话ID，同V$SESSION视图的AUDSID字段 |
| SID                        | 会话ID                                                       |
| YASDB_HOME                 | YASDB_HOME主目录的完整路径名                                 |
| CURRENT_EDITION_ID | 仅用于兼容，其值恒为空 |
| SESSION_EDITION_ID | 仅用于兼容，其值恒为空 |

**length**

指定返回值的长度，单位为字节，须为数值型或可隐式转换为数值型的其他类型。可省略，默认值为256。

- length的值域为INT类型范围（超出将报错），但本函数返回值的合法长度区间为[1,4000]，length值不在此区间时将按默认值256处理。

- length值统一按整数处理，若指定为小数，会直接截断小数取整。


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

SELECT SYS_CONTEXT('USERENV', 'DB_UNIQUE_NAME') res FROM DUAL;

RES
----------------------------------------------------------------
yashandb1-1:1
```
