## 功能简介

将源二进制串赋给yacRaw。返回YAC_SUCCESS表示赋值成功，返回YAC_ERROR表示赋值失败。

## 函数声明

```c
YacResult yacRawAssignBytes(YacHandle hEnv, const YacUint8* raw, YacUint32 rawLen, YacRaw** yacRaw);
```

## 参数说明

|  参数名| 说明|
| ------------------ | ---------------------- |
| hEnv (IN)          | 环境句柄。             |
| str (IN)           | 源二进制串。     |
| strLen (IN)        | 源二进制串长度。       |
| yacSrcRaw (IN/OUT) | 要被修改的yacRaw句柄。 |

如果要初始化yacRaw，必须首先调用yacRawAssignBytes或者yacRawResize。
