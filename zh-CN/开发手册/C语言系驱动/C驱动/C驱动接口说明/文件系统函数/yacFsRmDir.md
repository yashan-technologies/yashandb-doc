## 功能简介

yacFsRmDir函数用于在当前设置的卷下删除指定目录。

## 函数声明

```c
YacResult yacFsRmDir (YacHandle hConn,
                      YacChar*  name,
                      YacBool   cascade);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 待删除的目录名称（路径，已存在的目录）。                       |
| cascade (IN)      | 是否级联删除。<br>YAC_TRUE：递归删除目录及其所有子目录和文件。<br>YAC_FALSE：仅删除空目录。 |
