## 功能简介

yacDSIntervalFromText函数用于用于将源文本字符串赋给yacDSInterval，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacDSIntervalFromText(YacHandle hEnv,
                                YacDSInterval* dsInterval,
                                const YacChar* str,
                                YacUint32 strLen);
```

## 参数说明

| 参数名                  | 说明                |
|----------------------|-------------------|
| hEnv (IN)            | 环境句柄。             |
| dsInterval (IN/OUT)  | 要被修改的YacDSInterval句柄。|
| str (IN)             | 源字符串。             |
| strLen (IN)          | 源字符串长度（单位：字节）。    |

