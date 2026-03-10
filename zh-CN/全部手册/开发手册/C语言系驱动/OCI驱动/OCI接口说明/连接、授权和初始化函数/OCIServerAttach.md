## 功能简介

OCIServerAttach函数用于创建数据源的访问路径。

## 函数声明

```c
sword OCIServerAttach ( OCIServer     *srvhp,
                        OCIError      *errhp,
                        const OraText *dblink,
                        sb4            dblink_len,
                        ub4            mode );
```

## 参数说明

|  参数名| 说明|
| --------------- | ------------------------------------------------------------ |
| srvhp (IN/OUT)  | 一个未初始化的服务器句柄。<br />由该调用初始化。传入初始化的服务器句柄会导致错误。 |
| errhp (IN/OUT)  | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| dblink (IN)     | 指定要使用的数据库服务器。<br />此参数指向指定连接字符串或服务点的字符串。 |
| dblink_len (IN) | dblink指向的字符串的长度。                                   |
| mode (IN)       | 指定操作模式。                                                 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
|------------|--------------|
| dblink     | 非NULL        |
| dblink_len | 非0           |
| mode       | OCI_DEFAULT  |
