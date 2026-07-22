## 功能简介

OCILobTrim2函数是OCILobTrim函数的扩展版本，用于将LOB值截断为较短的长度。与OCILobTrim的主要区别在于使用oraub8类型的newlen参数替代ub4类型，支持超过4GB的LOB截断操作。

## 函数声明

```c
sword OCILobTrim2 ( OCISvcCtx       *svchp,
                    OCIError        *errhp,
                    OCILobLocator   *locp,
                    oraub8           newlen );
```

## 参数说明

|  参数名| 说明|
|----------------|------------------------------------------------------------|
| svchp (IN/OUT) | 服务上下文句柄。                                                   |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                     |
| locp (IN)      | 唯一引用LOB的LOB定位器。                                            |
| newlen (IN)    | LOB值的新长度，新长度必须小于等于当前LOB长度。对于字符LOB，它是字符数，对于BLOB，它是LOB中的字节数。 |
