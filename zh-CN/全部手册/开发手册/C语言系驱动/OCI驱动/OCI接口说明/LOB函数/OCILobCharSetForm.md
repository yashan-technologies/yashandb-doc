## 功能简介

OCILobCharSetForm函数用于获取LOB的字符集形式。

## 函数声明

```c
sword OCILobCharSetForm ( OCIEnv                *envhp, 
                          OCIError              *errhp, 
                          const OCILobLocator   *locp, 
                          ub1                   *csfrm );
```

## 参数说明

|  参数名| 说明|
| ------------ |---------------------------------------------------------------------------------------------------------------------------------------------------|
| envhp (IN/OUT) | 环境句柄。                                                                                                                                           |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                      |
| locp (IN)  | 唯一引用LOB的内部LOB定位器。                      |
| csfrm (OUT)  | 输出LOB的字符集形式，不能为NULL。返回SQLCS_IMPLICIT表示数据库字符集ID，默认为该值，返回SQLCS_NCHAR表示NCHAR字符集ID。<br />如果输入LOB类型为BLOB，由于二进制BLOB没有字符集的概念，csfrm返回0。 |

部分参数目前仅支持以下取值：

|  返回参数| 有效值|
|-------|-----------------------------|
| csfrm | SQLCS_IMPLICIT<br/>SQLCS_NCHAR<br/>0 |
