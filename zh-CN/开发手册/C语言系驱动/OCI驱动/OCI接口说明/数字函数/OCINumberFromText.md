## 功能简介

OCINumberFromText函数用于将字符串换为OCI NUMBER类型。

## 函数声明

```c
sword OCINumberFromText ( OCIError           *err, 
                          const OraText      *str, 
                          ub4                str_length,
                          const OraText      *fmt,
                          ub4                fmt_length, 
                          const OraText      *nls_params, 
                          ub4                nls_p_length, 
                          OCINumber          *number );
```

## 参数说明

|  参数名| 说明|
|-----------------|---------------------------------------------------------------------------------------------------|
| err (IN/OUT)    | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| str (IN)        | 要转换字符串的指针。                                                                                        |
| str_length (IN) | 字符串的长度。                                                                                           |
| fmt (IN)        | 格式化字符串，指定转换的格式，支持以下字符的组合：<br />- 小数点：`.`，例如'99.99'<br />- 美元符号：`$`，例如'$9,999'<br />- 强迫零显示：`0`，例如'00000'<br />- 指定位置返回数字：`9`，例如'9999'<br />- 指定位置返回小数点：`D`，例如'99D99'<br />- 设首位或末尾为-或+：`S`，例如'S9999'<br />- 千位分隔符：`，`，例如'9,999'<br />- 在指定位置返回千位分隔符：`G`，例如'9G9'<br />- 去掉前后空格和小数后面多余的0：`FM`，例如'FM999' |
| fmt_length (IN) | 转换格式的长度。                                                                                            |
| nls_params (IN) | 全球化支持格式规范字符串，用于指定数字格式的本地化字符。如果它是NULL字符串（(YacChar*)0），则使用会话的默认参数。<br />格式如：'NLS_NUMERIC_CHARACTERS=''dg'''，其中d为小数点字符，g为千位分隔符。例如：<br />- 'NLS_NUMERIC_CHARACTERS=''.,'''：句点作为小数点，逗号作为千位分隔符<br />- 'NLS_NUMERIC_CHARACTERS='''',.'''：逗号作为小数点，句点作为千位分隔符               |
| nls_p_length (IN) | 全球化支持格式规范的长度。                                                                                            |
| number  (OUT)   | 将给定的字符串转换为OCI NUMBER类型。                                                                           |
