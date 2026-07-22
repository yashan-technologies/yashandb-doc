## 功能简介

将源文本字符串赋给yacString。返回YAC_SUCCESS表示赋值成功，返回YAC_ERROR表示赋值失败。

## 函数声明

```c
YacResult yacStringAssignText(YacHandle hEnv, const YacChar* str, YacUint32 strLen, YacString** yacString);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------ |
| hEnv (IN)             | 环境句柄。                     |
| str (IN)              | 源字符串。               |
| strLen (IN)           | 源字符串长度（以字节为单位）。 |
| yacSrcString (IN/OUT) | 要被修改的yacString句柄。      |

如果要初始化yacString，必须首先调用yacStringAssignText或者yacStringResize。
