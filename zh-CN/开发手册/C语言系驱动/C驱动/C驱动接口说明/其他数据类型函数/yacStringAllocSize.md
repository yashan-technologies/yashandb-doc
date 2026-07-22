## 功能简介

获取yacString内存的分配大小（以字节为单位）。返回YAC_SUCCESS表示分配成功，返回YAC_ERROR表示分配失败。

## 函数声明

```c
YacResult yacStringAllocSize(YacHandle hEnv, const YacString* yacString, YacUint32* allocSize);
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------- |
| hEnv (IN)      | 环境句柄。                |
| yacString (IN) | yacString句柄。           |
| allocSize(OUT) | 存放yacString的内存大小。 |
