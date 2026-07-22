## Functionality Introduction

The yacSetEnvAttr function is used to set a certain attribute value in the environment information handle. It returns YAC_SUCCESS to indicate successful setting, and YAC_ERROR to indicate failure.

Environment-level parameters will affect the behavior of all handles under the current environment information handle. Please configure and retrieve them using yacSetEnvAttr() and [yacGetEnvAttr](yacGetEnvAttr) function.

|**Attribute** |Corresponding Data Type |Read/Write Privilege (R/W) |Description |
| --------------------- | ----------------- | ------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| YAC_ATTR_ENV_LOGIN_TIMEOUT         | YacUint32             | R/W                         | This value specifies the login process timeout for the current environment (unit: seconds), defaults to 300. |
| YAC_ATTR_CHARSET_CODE            | YacCharsetCode          | R/W                         | This value specifies the client character set of the current environment:<br />YAC_CHARSET_ASCII = ASCII character set.<br />YAC_CHARSET_GBK = GBK character set.<br />YAC_CHARSET_UTF8 = UTF8 character set.<br />YAC_CHARSET_ISO88591 = ISO88591 character set.<br />YAC_CHARSET_GB18030 = GB18030 character set. |
| YAC_ATTR_RETURN_SUCCESS_WITH_INFO | YacBool                 | R/W                         | This value specifies whether the current environment needs to support API returning YAC_SUCCESS_WITH_INFO return value. This value only supports YAC_TRUE, YAC_FALSE.                                                                 |
| YAC_ATTR_NCHAR_LITERAL_REPLACE    | YacBool                 | R/W                         | This value specifies whether to enable NCHAR literal replacement functionality in the current environment. This value only supports YAC_TRUE, YAC_FALSE.                                                                 |

## Function Declaration

```c
YacResult yacSetEnvAttr(YacHandle hEnv,
                        YacEnvAttr attr,
                        YacVoid* value,
                        YacInt32 length);
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ---------------------------- |
| hEnv (IN/OUT)  | Environment handle.          |
| attr (IN)      | Attribute type.              |
| value (IN)     | The attribute value to be set. |
| length (IN)    | The cache length of the attribute value to be set. |