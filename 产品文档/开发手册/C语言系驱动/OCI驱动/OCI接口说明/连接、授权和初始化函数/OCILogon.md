## 功能简介

OCILogon函数用于获取一个已经连接的用户会话。

## 函数声明

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

## 参数说明

| 参数名         | 说明                                                    |
| -------------- | ------------------------------------------------------- |
| envhp(IN)      | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。 |
| errhp (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| svchp(IN/OUT)  | 服务上下文的句柄。                                        |
| username(IN)   | 登录的用户名。                                            |
| uname_len(IN)  | 用户名长度。                                              |
| password(IN)   | 登录的密码。                                              |
| passwd_len(IN) | 密码长度。                                                |
| dbname(IN)     | 指定要使用的数据库服务器。<br />此参数指向指定连接字符串或服务点的字符串。 |
| dbname_len(IN) | dbname的长度。                                            |
