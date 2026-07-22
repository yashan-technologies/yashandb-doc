## 功能简介

yacBindParameter函数用于客户端对指定参数的属性绑定赋值，返回YAC_SUCCESS表示绑定成功，返回YAC_ERROR表示绑定失败。

## 函数声明

```c
yacResult yacBindParameter ( yacHandle  hStmt,
                             yacUint16  id,
                             YacParamDirection direction,
                             YacType bindType,
                             yacPointer value,
                             YacInt32 bindSize,
                             YacInt32 bufLength,
                             yacInt32*  indicator);
```

## 参数说明

|  参数名| 说明|
| ------------------ | ------------------------------------------------------------ |
| hStmt (IN/OUT)     | 记录与SQL相关的多种信息的句柄。                              |
| id (IN)            | 参数的编号索引，1表示第1个参数。预编译SQL中如果使用`:name`形式占位符且多处同名，按位置绑定值时需要绑定多次。通常情况下，同名占位符对应位置绑定的是相同值。 |
| direction (IN)     | 参数类型，<br />- YAC_PARAM_INPUT (1)：入参<br />- YAC_PARAM_OUTPUT (2)：出参<br />- YAC_PARAM_INPUT_OUTPUT (3)：出入参      |
| bindType (IN)      | 需要绑定到该参数的类型。                                       |
| value (IN)         | 需要绑定到该参数的值。                                         |
| bindSize (IN)      | 需要绑定到该参数的值所在缓冲区长度。                           |
| bufLength (IN)     | 保留参数，暂不生效。                                       |
| indicator (IN/OUT) | 辅助指针。<br/>作为入参：表示绑定到id列的变长类型值的长度，对非变长类型（包括使用loblocator绑定的类型）不生效。当赋值为YAC_NULL_DATA时，表示要绑定的数据为NULL。<br/>作为出参：表示结果集取值情况。<br/>当赋值为NULL时，表示该指针不生效。 |
