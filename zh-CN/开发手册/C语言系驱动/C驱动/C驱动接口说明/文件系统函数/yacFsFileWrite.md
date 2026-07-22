## 功能简介

yacFsFileWrite函数用于在当前设置的卷下将数据写入指定文件。写入操作采用流式传输方式，支持大文件分块写入。

> **Caution**:
>
> - 写入操作会覆盖文件原有内容（truncate模式）。
> - 在未开启多版本时，旧文件内容将不再存在。

## 函数声明

```c
YacResult yacFsFileWrite (YacHandle      hConn,
                          YacChar*       name,
                          YacFsStream*   stream,
                          YacUint32*     wrote);
```

## 参数说明

|  参数名| 说明|
| --- | --- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 目标文件名称（路径，已存在的文件）。                           |
| stream (IN)       | 数据流对象，提供写入的数据。其结构体定义详见下文介绍。                      |
| wrote (OUT)       | 实际写入的字节数。                                           |

YacFsStream结构体的定义如下表所示：

|  字段名  |  类型  |  说明  |
| --- | --- | --- |
| ctx       | YacPointer     | 上下文指针。                                           |
| buf       | YacUint8*      | 数据缓冲区。                                           |
| bufLen    | YacUint64      | 缓冲区大小。                                           |
| read      | 函数指针       | 读取回调函数，签名：`YacUint32 (*read)(YacFsStream* stream)` |
