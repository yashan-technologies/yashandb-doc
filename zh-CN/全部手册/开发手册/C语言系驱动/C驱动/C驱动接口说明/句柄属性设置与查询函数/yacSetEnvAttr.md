## 功能简介

yacSetEnvAttr函数用于设置环境信息句柄中的某个属性值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

env级别的参数会对当前环境信息句柄下的所有句柄的行为产生影响，请通过yacSetEnvAttr()和[yacGetEnvAttr](yacGetEnvAttr)函数进行配置和获取。

|  **属性**| value对应数据类型| 属性读写权限（R/W）| 说明|
| --------------------- | ----------------- | ------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| YAC_ATTR_CHARSET_CODE | YacCharsetCode    | R/W                 | 该值指定当前环境的客户端字符集：<br />YAC_CHARSET_ASCII = ASCII字符集。<br />YAC_CHARSET_GBK = GBK字符集。<br />YAC_CHARSET_UTF8 = UTF8字符集。<br />YAC_CHARSET_ISO88591 = ISO88591字符集。<br />YAC_CHARSET_GB18030 = GB18030字符集。 |
| YAC_ATTR_RETURN_SUCCESS_WITH_INFO | YacBool        | R/W                 | 该值指定当前环境是否需要支持API返回YAC_SUCCESS_WITH_INFO返回值，该值只支持YAC_TRUE，YAC_FALSE。                                                                                         |
| YAC_ATTR_NCHAR_LITERAL_REPLACE | YacBool        | R/W                 | 该值指定当前环境是否开启NCHAR字面量替换功能，该值只支持YAC_TRUE，YAC_FALSE。                                                                                         |

## 函数声明

```c
YacResult yacSetEnvAttr(YacHandle hEnv,
                        YacEnvAttr attr,
                        YacVoid* value,
                        YacInt32 length);
```

## 参数说明

|  参数名| 说明|
| ------------- | ---------------------------- |
| hEnv (IN/OUT) | 环境句柄。                   |
| attr (IN)     | 属性类型。                   |
| value (IN)    | 欲设置的属性值。             |
| length (IN)   | 欲设置的属性值的缓冲区长度。 |
