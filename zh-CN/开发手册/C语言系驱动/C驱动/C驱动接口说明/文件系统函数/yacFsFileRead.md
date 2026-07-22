## 功能简介

yacFsFileRead函数用于在当前设置的卷下从指定位置读取文件数据。读取操作支持大文件分块传输。

## 函数声明

```c
YacResult yacFsFileRead (YacHandle hConn,
                         YacChar*  name,
                         YacUint64 offset,
                         YacUint8* buf,
                         YacUint32 bufLen,
                         YacUint32* read);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 目标文件名称（路径，已存在的文件）。                           |
| offset (IN)       | 读取起始位置（单位：字节）。                                 |
| buf (OUT)         | 用于存储读取数据的缓冲区。                                   |
| bufLen (IN)       | 缓冲区大小。                                                 |
| read (OUT)        | 实际读取的字节数。                                           |

