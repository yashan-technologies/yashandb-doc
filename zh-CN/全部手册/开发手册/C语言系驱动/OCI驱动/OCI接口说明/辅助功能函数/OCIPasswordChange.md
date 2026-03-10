## 功能简介

OCIPasswordChange函数用于更改用户密码。

## 函数声明

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

## 参数说明

|  参数名| 说明|
| ----------- | -------------------------------------- |
| svchp(IN)   | 初始化的服务器上下文的句柄。             |
| errhp(IN)   | 错误句柄，当出现错误时可以获取诊断信息。 |
| user_name   | 用户名。                                 |
| usernm_len  | 用户名长度。                             |
| opasswd     | 旧密码。                                 |
| opasswd_len | 旧密码长度。                             |
| npasswd     | 新密码。                                 |
| npasswd_len | 新密码长度。                             |
| mode(IN)    | 模式，保留参数。                     |

部分参数目前仅支持以下取值：

|  参数| 有效值|
|-------------|---------------------------|
| user_name   | 非NULL                     |
| usernm_len  | 非0                        |
| opasswd     | 非NULL                     |
| opasswd_len | 非0                        |
| npasswd     | 非NULL                     |
| npasswd_len | 非0                        |
| mode        | OCI_AUTH<br />OCI_DEFAULT |
