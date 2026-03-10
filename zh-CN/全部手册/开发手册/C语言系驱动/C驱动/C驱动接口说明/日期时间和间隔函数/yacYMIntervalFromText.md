## 功能简介

yacYMIntervalFromText函数用于将源文本字符串赋给yacYMInterval，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacYMIntervalFromText(YacHandle hEnv,
                                YacYMInterval* ymInterval,
                                const YacChar* str,
                                YacUint32 strLen);
```

## 参数说明

|  参数名| 说明|
| --------------- |-----------------------|
| hEnv (IN)            | 环境句柄。                 |
| ymInterval (IN/OUT)  | 要被修改的YacYMInterval句柄。 |
| str (IN)             | 源字符串。              |
| strLen (IN)          | 源字符串长度（单位：字节）。        |
