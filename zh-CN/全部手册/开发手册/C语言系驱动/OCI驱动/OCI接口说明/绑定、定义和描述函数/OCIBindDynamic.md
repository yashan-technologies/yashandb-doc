## 功能简介

OCIBindDynamic函数用于注册动态数据分配的用户回调。

目前仅支持回调函数返回OCI_CONTINUE，仅支持\*piecep赋值为OCI_ONE_PIECE。

## 函数声明

```c
sword OCIBindDynamic ( OCIBind     *bindp,
                       OCIError    *errhp,
                       void        *ictxp, 
                       OCICallbackInBind         (icbfp)(
                                void             *ictxp,
                                OCIBind          *bindp,
                                ub4               iter, 
                                ub4               index, 
                                void            **bufpp,
                                ub4              *alenp,
                                ub1              *piecep, 
                                void            **indpp ),
                                void             *octxp,
                       OCICallbackOutBind        (ocbfp)(
                                void             *octxp,
                                OCIBind          *bindp,
                                ub4               iter, 
                                ub4               index, 
                                void            **bufpp, 
                                ub4             **alenpp,
                                ub1              *piecep,
                                void            **indpp, 
                                ub2             **rcodepp ) );
```

## 参数说明

|  参数名| 说明|
| -------------- |------------------------------|
| bindp (IN/OUT) | 绑定句柄。                        |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。       |
| ictxp (IN)     | 回调函数icbfp所需的上下文指针。         |
| icbfp (IN)     | 在运行时返回指向IN绑定值或片段的指针的回调函数。  |
| octxp (IN)     | 回调函数ocbfp所需的上下文指针，保留参数，暂不使用。         |
| ocbfp (IN)     | 在运行时返回指向OUT绑定值或片段的指针的回调函数，保留参数，暂不使用。 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- |----------------|
| icbfp | 非NULL          |
| octxp | NULL，保留参数，暂不使用 |
| ocbfp | NULL，保留参数，暂不使用 |
