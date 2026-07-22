## 功能简介

yacVectorToText函数用于将yacVector对象转换为文本字符串。返回YAC_SUCCESS表示转换成功，返回YAC_ERROR表示转换失败。

## 函数声明

```c
YacResult yacVectorToText(YacVector* vector,
                          YacChar* text,
                          YacUint32* textlen,
                          YacUint32 mode);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| vector (IN)    | 用于转换字符串的源vector描述符。 |
| text (OUT)     | 用于接收转换后字符串的缓冲区起始地址。 |
| textlen (OUT)  | 转换后字符串的实际长度。 |
| mode (IN)      | 预留参数。 |
