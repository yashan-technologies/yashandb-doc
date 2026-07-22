## 功能简介

yacFsSetCurrentVolume函数用于设置当前连接使用的卷。设置后，后续的文件操作（创建、删除、读写等）均在该卷下进行。

## 函数声明

```c
YacResult yacFsSetCurrentVolume (YacHandle hConn,
                                 YacChar*  name);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 目标卷名称（已存在的卷）。                                    |
