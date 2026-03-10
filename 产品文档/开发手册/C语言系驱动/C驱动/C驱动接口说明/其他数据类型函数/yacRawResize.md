## 功能简介

调整给定yacRaw的内存大小。返回YAC_SUCCESS表示调整成功，返回YAC_ERROR表示调整失败。

## 函数声明

```c
YacResult yacRawResize(YacHandle hEnv, YacUint32 newSize, YacRaw** yacRaw);
```

## 参数说明

| 参数名             | 说明                 |
| ------------------ | -------------------- |
| hEnv (IN)          | 环境句柄。           |
| newSize (IN)       | 要调整到的内存大小。 |
| yacRaw (IN/OUT)    | yacRaw句柄。         |

如果要初始化yacRaw，必须首先调用yacRawAssignBytes或者yacRawResize。

如果要释放，必须调用yacRawResize，newSize赋0来释放。