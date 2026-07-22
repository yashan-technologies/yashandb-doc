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
| fmt (IN)        | 格式化字符串，指定转换的格式，支持以下字符的组合：<br />- 小数点：`.`，例如'99.99'<br />- 美元符号：`$`，例如'$9,999'<br />- 强迫零显示：`0`，例如'00000'<br />- 指定位置返回数字：`9`，例如'9999'<br />- 指定位置返回小数点：`D`，例如'99D99'<br />- 设首位或末尾为-或+：`S`，例如'S9999'<br />- 千位分隔符：`，`，例如'9,999'<br />- 在指定位置返回千位分隔符：`G`，例如'9G9'<br />- 去掉前后空格和小数后面多余的0：`FM`，例如'FM999'<br />- 当整数部分为零时返回空格：`B`，例如'B99'<br />- 在指定位置返回货币符号：`C`，例如'C99'<br />- 以科学计数法返回：`EEEE`，例如'9EEEE'<br />- 显示货币符号：`L`，例如'L99'<br />- 负值末尾填充负号：`MI`，例如'9MI'<br />- 负值放在<>中：`PR`，例如'9PR'<br />- 返回十六进制值：`X`，例如'FFF'         |
| fmtLength (IN)  | 格式化字符串的长度（单位：字符）。                                 |
| nlsParam (IN)   | 全球化支持格式规范字符串，用于指定数字格式的本地化字符。如果它是NULL字符串（(YacChar*)0），则使用会话的默认参数。<br />格式如：'NLS_NUMERIC_CHARACTERS=''dg'''，其中d为小数点字符，g为千位分隔符。例如：<br />- 'NLS_NUMERIC_CHARACTERS=''.,'''：句点作为小数点，逗号作为千位分隔符<br />- 'NLS_NUMERIC_CHARACTERS='''',.'''：逗号作为小数点，句点作为千位分隔符 |
| nlsParamLength (IN) | 全球化支持格式规范字符串的长度（单位：字符），如果值为0，则使用会话的默认参数。          |
| str (OUT)      | 输出字符串缓冲区。                                         |
| bufLength (IN) | 字符串缓冲区长度。                                         |
| length (OUT)   | 用于获取字符串实际长度，可为NULL表示不需要获取字符串实际长度。                 |
