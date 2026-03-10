## 功能简介

调整给定yacString的内存大小。返回YAC_SUCCESS表示调整成功，返回YAC_ERROR表示调整失败。

## 函数声明

```c
YacResult yacStringResize(YacHandle hEnv, YacUint32 newSize, YacString** yacString);
```

## 参数说明

| 参数名                | 说明                                 |
| --------------------- | ------------------------------------ |
| hEnv (IN)             | 环境句柄。                           |
| newSize (IN)          | 要调整到的内存大小（以字节为单位）。 |
| yacString (IN/OUT)    | yacString句柄。                      |

如果要初始化yacString，必须首先调用yacStringAssignText或者yacStringResize。

如果要释放，必须调用yacStringResize，newSize赋0来释放。