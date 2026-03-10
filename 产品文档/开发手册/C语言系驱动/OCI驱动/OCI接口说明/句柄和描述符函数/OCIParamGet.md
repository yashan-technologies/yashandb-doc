## 功能简介

OCIParamGet函数用于返回由描述句柄或语句句柄中的位置指定的参数的描述符。

## 函数声明

```c
sword OCIParamGet ( const void        *hndlp,
                    ub4                htype,
                    OCIError          *errhp,
                    void             **parmdpp,
                    ub4                pos );
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| hndlp (IN)     | 语句句柄或描述句柄。<br />OCIParamGet函数返回此句柄的参数描述符。 |
| htype (IN)     | 在hndlp参数中传递的句柄的类型。                              |
| errhp (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| parmdpp (OUT)  | 位于pos参数中给定位置的参数描述符，句柄类型为OCI_type_PARAM |
| pos (IN)       | 语句句柄或描述句柄中的位置号。<br />将为此位置返回一个参数描述符。 |

部分参数目前仅支持以下取值：

| 参数  | 有效值                              |
| ----- | ----------------------------------- |
| htype | OCI_HTYPE_STMT<br />OCI_DTYPE_PARAM<br />OCI_HTYPE_ERROR |

