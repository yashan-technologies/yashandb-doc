## 功能简介

yacNumberFromText函数用于将源文本字符串赋给YacNumber，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacNumberFromText(const YacChar* str,
                         YacUint32 strLength,
                         const YacChar* fmt,
                         YacUint32 fmtLength,
                         const YacChar* nlsParam,
                         YacUint32 nlsParamLength,
                         YacNumber* number);
```

## 参数说明

| 参数名             | 说明                                                |
|-----------------|---------------------------------------------------|
| str (IN)        | 源文本字符串。                                           |
| strLength (IN)  | 源文本字符串的长度（单位：字符）。                                 |
| fmt (IN)        | 格式化字符串。                                           |
| fmtLength (IN)  | 格式化字符串的长度（单位：字符）。                                 |
| nlsParam (IN)   | 全球化支持格式规范字符串，如果它是NULL字符串（（YacChar*）0），则使用会话的默认参数。 |
| nlsParamLength (IN) | 全球化支持格式规范字符串的长度（单位：字符），如果值为0，则使用会话的默认参数。          |
| number (IN/OUT) | 要被修改的YacNumber句柄。                                 |