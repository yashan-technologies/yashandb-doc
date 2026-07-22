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

|  参数名| 说明|
|-----------------|---------------------------------------------------|
| str (IN)        | 源文本字符串。                                           |
| strLength (IN)  | 源文本字符串的长度（单位：字符）。                                 |
| fmt (IN)        | 格式化字符串，指定转换的格式，支持以下字符的组合：<br />- 小数点：`.`，例如'99.99'<br />- 美元符号：`$`，例如'$9,999'<br />- 强迫零显示：`0`，例如'00000'<br />- 指定位置返回数字：`9`，例如'9999'<br />- 指定位置返回小数点：`D`，例如'99D99'<br />- 设首位或末尾为-或+：`S`，例如'S9999'<br />- 千位分隔符：`，`，例如'9,999'<br />- 在指定位置返回千位分隔符：`G`，例如'9G9'<br />- 去掉前后空格和小数后面多余的0：`FM`，例如'FM999'     |
| fmtLength (IN)  | 格式化字符串的长度（单位：字符）。                                 |
| nlsParam (IN)   | 全球化支持格式规范字符串，用于指定数字格式的本地化字符。如果它是NULL字符串（(YacChar*)0），则使用会话的默认参数。<br />格式如：'NLS_NUMERIC_CHARACTERS=''dg'''，其中d为小数点字符，g为千位分隔符。例如：<br />- 'NLS_NUMERIC_CHARACTERS=''.,'''：句点作为小数点，逗号作为千位分隔符<br />- 'NLS_NUMERIC_CHARACTERS='''',.'''：逗号作为小数点，句点作为千位分隔符 |
| nlsParamLength (IN) | 全球化支持格式规范字符串的长度（单位：字符），如果值为0，则使用会话的默认参数。          |
| number (IN/OUT) | 要被修改的YacNumber句柄。       |
