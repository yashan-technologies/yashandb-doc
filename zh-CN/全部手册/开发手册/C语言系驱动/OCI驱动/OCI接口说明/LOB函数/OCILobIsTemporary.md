## 功能简介

OCILobIsTemporary函数用于判断LOB是否是临时LOB。

## 函数声明

```c
sword OCILobIsTemporary(OCIEnv            *envhp,
                        OCIError          *errhp,
                        OCILobLocator     *locp,
                        boolean           *is_temporary);
```

## 参数说明

|  参数名| 说明|
| ----------- |---------------------------------------------|
| envhp (IN) | 环境句柄。                                     |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                       |
| locp (IN)  | 唯一引用LOB的内部LOB定位器。 |
| is_temporary (OUT)  | 如果是临时LOB返回TRUE，否则返回FALSE。                   |
