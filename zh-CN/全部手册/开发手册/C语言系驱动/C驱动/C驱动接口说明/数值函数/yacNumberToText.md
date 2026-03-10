## 功能简介

yacNumberToText函数用于将YacNumber类型转换为String类型，返回YAC_SUCCESS表示转换成功，返回YAC_ERROR表示转换失败。

## 函数声明

```c
YacResult yacNumberToText(const YacNumber* number,
                         const YacChar* fmt,
                         YacUint32 fmtLength,
                         const YacChar* nlsParam,
                         YacUint32 nlsParamLength,
                         YacChar* str,
                         YacInt32 bufLength,
                         YacInt32* length);
```

## 参数说明

|  参数名| 说明|
| -------------- |---------------------------------------------------|
| number (IN)     | 源YacNumber句柄。                                     |
| fmt (IN)        | 格式化字符串。                                           |
| fmtLength (IN)  | 格式化字符串的长度（单位：字符）。                                 |
| nlsParam (IN)   | 全球化支持格式规范字符串，如果它是NULL字符串（（YacChar*）0），则使用会话的默认参数。 |
| nlsParamLength (IN) | 全球化支持格式规范字符串的长度（单位：字符），如果值为0，则使用会话的默认参数。          |
| str (OUT)      | 输出字符串缓冲区。                                         |
| bufLength (IN) | 字符串缓冲区长度。                                         |
| length (OUT)   | 用于获取字符串实际长度，可为NULL表示不需要获取字符串实际长度。                 |
