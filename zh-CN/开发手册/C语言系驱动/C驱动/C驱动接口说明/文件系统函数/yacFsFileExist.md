## 功能简介

yacFsFileExist函数用于在当前设置的卷下判断指定文件是否存在。

## 函数声明

```c
YacResult yacFsFileExist (YacHandle hConn,
                          YacChar*  name,
                          YacBool*  exist);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 待检查的文件名称（路径，已存在的文件）。                       |
| exist (OUT)       | 文件是否存在。<br>YAC_TRUE：文件存在。<br>YAC_FALSE：文件不存在。 |
