## 功能简介

在其他函数调用返回YAC_ERROR或YAC_SUCCESS_WITHINFO时，通过yacGetDiagRec函数可以获取相应的诊断信息。

## 函数声明

```c
YacResult yacGetDiagRec(YacInt32* errCode,
                        YacChar* message,
                        YacInt32 bufLen,
                        YacInt32* incicator,
                        YacChar* sqlState,
                        YacInt32 sqlStateBufLen,
                        YacTextPos* pos);
```

## 参数说明

| 参数名              | 说明                            |
| ------------------- | ------------------------------- |
| errCode (IN/OUT)    | YashanDB错误码序号。            |
| message (IN/OUT)    | 错误码信息buf地址。             |
| bufLen (IN)         | 错误码信息buf大小。             |
| incicator (IN/OUT)  | 错误码信息buf实际填充大小指针。 |
| sqlState (IN/OUT)   | SQL标准错误码buf地址。          |
| sqlStateBufLen (IN) | SQL标准错误码buf大小。          |
| pos (IN/OUT)        | 错误出现的行列位置。            |

