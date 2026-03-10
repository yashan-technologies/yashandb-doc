## 功能简介

OCILobGetChunkSize函数用于获取LOB的块大小。

## 函数声明

```c
sword OCILobGetChunkSize ( OCISvcCtx       *svchp,
                           OCIError        *errhp,
                           OCILobLocator   *locp,
                           ub4             *chunk_size );
```

## 参数说明

| 参数名         | 说明                                                                             |
| ----------- |--------------------------------------------------------------------------------|
| svchp (IN) | 服务上下文句柄。                                                                        |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                                          |
| locp (IN/OUT)  | 唯一引用LOB的内部LOB定位器。                                    |
| chunk_size (OUT)  | 用于存储内部LOB值的块空间大小，即用户在读取或写入LOB值时建议使用的空间大小，以字节表示。 |
