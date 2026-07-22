## 功能简介

yacFsGetCurrentVolume函数用于获取当前连接使用的卷名称。

## 函数声明

```c
YacResult yacFsGetCurrentVolume (YacHandle hConn,
                                 YacChar*  name,
                                 YacUint32 bufSize);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (OUT)        | 用于存储当前卷名称的缓冲区。                                 |
| bufSize (IN)      | 缓冲区大小。                                                 |

