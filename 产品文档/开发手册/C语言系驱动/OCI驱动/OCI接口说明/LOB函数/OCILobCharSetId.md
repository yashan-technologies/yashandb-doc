## 功能简介

OCILobCharSetId函数用于获取LOB的字符集ID。

## 函数声明

```c
sword OCILobCharSetId ( OCIEnv                  *envhp, 
                        OCIError                *errhp, 
                        const OCILobLocator     *locp, 
                        ub2                     *csid );
```

## 参数说明

| 参数名         | 说明                                                                    |
| ----------- |-----------------------------------------------------------------------|
| envhp (IN/OUT) | 环境句柄。                                                                 |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                                |
| locp (IN)  | 唯一引用LOB的内部LOB定位器。                           |
| csid (OUT)  | 输出LOB的字符集ID，不能为NULL。<br />如果输入LOB类型为BLOB，由于二进制BLOB没有字符集的概念，csid返回0（YCI_INVALID_CHARSET）。 |

部分参数目前仅支持以下取值：

| 返回参数  | 有效值                                     |
|-------|-----------------------------------------|
| csid | OCI_UTF16ID<br/>YCI_UTF8ID<br/>YCI_ZHS16GBK<br/>0（YCI_INVALID_CHARSET） |