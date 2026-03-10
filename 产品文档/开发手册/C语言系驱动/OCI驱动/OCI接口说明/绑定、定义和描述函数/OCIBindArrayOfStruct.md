## 功能简介

OCIBindArrayOfStruct函数用于设置静态数组绑定的跳过参数，为可选项，可以设置批量绑定参数的步长。

## 函数声明

```c
sword OCIBindArrayOfStruct ( OCIBind     *bindp,
                             OCIError    *errhp,
                             ub4          pvskip, 
                             ub4          indskip, 
                             ub4          alskip, 
                             ub4          rcskip );
```

## 参数说明

| 参数名         | 说明                                       |
| -------------- | ------------------------------------------ |
| bindp (IN/OUT) | 绑定结构的句柄。                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| pvskip (IN)    | 用于下一个数据值的跳过参数。                 |
| indskip (IN)   | 用于下一个指示符值或结构的跳过参数。         |
| alskip (IN)    | 用于下一个实际长度值的跳过参数。             |
| rcskip (IN)    | 用于下一列级别返回代码值的跳过参数。         |

部分参数目前仅支持以下取值：

| 参数名    | 有效值         |
| ------- | --------------- |
| pvskip  | 除0以外的数值   |
| indskip | 0或其他合理数值 |
| alskip  | 0或其他合理数值 |
| rcskip  | 0或其他合理数值 |
