## 功能简介

如果在[OCIDefineByPos](OCIDefineByPos)或OCIDefineByPos2中选择OCI_DYNAMIC_FETCH模式，则需调用OCIDefineDynamic函数设置相应的附加属性。

目前仅支持回调函数返回OCI_CONTINUE，仅支持\*piecep赋值为OCI_ONE_PIECE。

## 函数声明

```c
sword OCIDefineDynamic ( OCIDefine   *defnp,
                         OCIError    *errhp,
                         void        *octxp, 
                         OCICallbackDefine      (ocbfp)(
                                  void          *octxp,
                                  OCIDefine     *defnp,
                                  ub4            iter, 
                                  void         **bufpp,
                                  ub4          **alenpp,
                                  ub1           *piecep,
                                  void         **indpp,
                                  ub2          **rcodep ) );
```

## 参数说明

|  参数名| 说明|
| -------------- | --------------------------------------------------------- |
| defnp (IN/OUT) | 定义结构句柄。                                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                 |
| octxp (IN)     | 指向回调函数的上下文。                                       |
| (IN)           | 指向回调函数。在运行时调用以获取指向缓冲区的指针，在该缓冲区中将检索所获取的数据或部分数据。回调还指定指标、返回码以及数据片和指标的长度。 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- | ------ |
| ocbfp | 非NULL |

目前输出数据缓冲区取数据不支持变长类型截断，取结果集数据时，所有变长类型遇到大小不足的输出数据缓冲区会报错。
