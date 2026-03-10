## 功能简介

yacBindParameterByName函数用于客户端按参数名对指定参数的属性绑定赋值，返回YAC_SUCCESS表示绑定成功，返回YAC_ERROR表示绑定失败。

## 函数声明

```c
yacResult yacBindParameterByName ( yacHandle  hStmt,
                                   YacChar*   name, 
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
| name (IN)          | 参数名。                                                     |
| direction (IN)     | 参数类型，1表示入参，2表示出参，3表示出入参。                      |
| bindType (IN)      | 需要绑定到该参数的类型。                                       |
| value (IN)         | 需要绑定到该参数的值。                                         |
| bindSize (IN)      | 需要绑定到该参数的限制长度。                                   |
| bufLength (IN)     | 需要绑定到该参数的值所在缓冲区长度。                           |
| indicator (IN/OUT) | 辅助指针。<br/>作为入参：表示绑定到id列的变长类型值的长度，对非变长类型（包括使用loblocator绑定的类型）不生效。当赋值为YAC_NULL_DATA时，表示要绑定的数据为NULL。<br/>作为出参：表示出参值的实际大小，若该参数值为YAC_NULL_DATA，则表示取出的数据为NULL。 |
