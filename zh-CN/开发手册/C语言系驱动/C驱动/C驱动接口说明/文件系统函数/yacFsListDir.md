## 功能简介

yacFsListDir函数用于在当前设置的卷下列出指定目录下的文件或目录。列表操作采用回调机制，支持分批获取目录内容。

> **Caution**:
>
> - 同一个连接不允许嵌套调用listdir（在回调函数内再次调用listdir）。
> - 服务端要求一次迭代显式完成后，才能开启下一次迭代。

## 函数声明

```c
YacResult yacFsListDir (YacHandle         hConn,
                        YacChar*          dir,
                        YacUint32         count,
                        YacIterFsEntry    iter,
                        YacPointer        ctx);
```

## 参数说明

|  参数名| 说明|
| --- | --- |
| hConn (IN)        | 连接句柄，标识与数据库的连接。                               |
| dir (IN)          | 目标目录名称（路径，已存在的目录）。                           |
| count (IN)        | 每次请求返回的最大条目数。                                   |
| iter (IN)         | 迭代回调函数，用于处理获取到的目录条目，其函数定义详见下文介绍。                     |
| ctx (IN)          | 用户上下文指针，会传递给回调函数。                           |

回调函数类型：

```c
typedef YacBool (*YacIterFsEntry) (YacPointer            ctx,
                                   const YacFsEntry*     entries,
                                   YacUint32             count);
```

YacFsEntry结构体的定义如下表所示：

|  字段名  |  类型  |  说明  |
| --- | --- | --- |
| type    | YacUint8   | 条目类型，其类型枚举详见后文介绍。                       |
| name    | YacChar[]  | 条目名称。                                               |

YacFsEntryType枚举的定义如下：

|  枚举值  |  说明  |
| --- | --- |
| YAC_FSENTRY_FILE | 普通文件。 |
| YAC_FSENTRY_DIR  | 目录。     |
