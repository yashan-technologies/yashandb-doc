## 功能简介

OCISessionPoolCreate函数用于创建会话池。

## 函数声明

```c
sword OCISessionPoolCreate ( OCIEnv           *envhp,
                             OCIError         *errhp, 
                             OCISPool         *spoolhp,
                             OraText         **poolName,
                             ub4              *poolNameLen,
                             const OraText    *connStr,
                             ub4               connStrLen,
                             ub4               sessMin, 
                             ub4               sessMax, 
                             ub4               sessIncr,
                             OraText          *userid,
                             ub4               useridLen,
                             OraText          *password,
                             ub4               passwordLen,
                             ub4               mode );
```

## 参数说明

| 参数名           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| envhp(IN)        | 环境句柄。                                                     |
| errhp(IN/OUT)    | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| spoolhp(IN/OUT)  | 会话池句柄。                                                   |
| poolName(OUT)    | 会话池名字。                                                   |
| poolNameLen(OUT) | 会话池名字长度。                                               |
| connStr(IN)      | 数据库服务器的连接字符串。                                     |
| connStrLen(IN)   | 数据库服务器的连接字符串长度。                                 |
| sessMin(IN)      | 会话池最小的会话个数，保留参数。                           |
| sessMax(IN)      | 会话池最大的会话个数，保留参数。                           |
| sessIncr(IN)     | 会话池动态扩展步长，保留参数。                             |
| userid(IN)       | 用户名。                                                       |
| useridLen(IN)    | 用户名长度。                                                   |
| password(IN)     | 密码。                                                         |
| passwordLen(IN)  | 密码长度。                                                     |
| mode(IN)         | 模式，保留参数。                                           |


部分参数目前仅支持以下取值：

| 参数          | 有效值                                |
|-------------|------------------------------------|
| poolName    | 非NULL                              |
| poolNameLen | 非0                                 |
| connStr     | 非NULL                              |
| connStrLen  | 非0                                 |
| userid      | 非NULL                              |
| useridLen   | 非0                                 |
| password    | 非NULL                              |
| passwordLen | 非0                                 |