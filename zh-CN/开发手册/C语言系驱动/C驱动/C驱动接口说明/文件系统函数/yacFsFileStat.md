## 功能简介

yacFsFileStat函数用于在当前设置的卷下获取指定文件的基本属性信息。

## 函数声明

```c
YacResult yacFsFileStat (YacHandle     hConn,
                         YacChar*      name,
                         YacFileStat*  stat);
```

## 参数说明

|  参数名| 说明|
| --- | --- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| name (IN)         | 目标文件名称（路径，已存在的文件）。                           |
| stat (OUT)        | 文件属性信息，其结构体定义详见后文介绍。                      |

YacFileStat结构体的定义如下表所示：

|  字段名  |  类型  |  说明  |
| --- | --- | --- |
| id            | YacUint64 | 文件ID。                                               |
| size          | YacUint64 | 文件大小（单位：字节）。                                 |
| createTime    | YacUint64 | 创建时间（时间戳）。                                     |
| modifyTime    | YacUint64 | 修改时间（时间戳）。                                     |
| type          | YacUint8  | 文件类型，其类型枚举详见[yacFsListDir中YacFsEntryType的定义](./yacFsListDir.md#参数说明)。|
