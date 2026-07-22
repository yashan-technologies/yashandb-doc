## 功能简介

yacBindColumn函数用于客户端对指定列的属性绑定赋值，返回YAC_SUCCESS表示绑定成功，返回YAC_ERROR表示绑定失败。

## 函数声明

```c
yacResult yacBindColumn ( yacHandle  hStmt,
                          yacUint16  id,
                          yacType    type,
                          yacPointer value,
                          yacInt32   bufLen,
                          yacInt32*  indicator);
```

## 参数说明

|  参数名| 说明|
| --------------- | ------------------------------------------------------------ |
| hStmt (IN/OUT)  | 记录与SQL相关的多种信息的句柄。                              |
| id (IN)         | 列的编号索引，0表示第1列。                                 |
| type (IN)       | 需要绑定到id列的类型。                                         |
| value (IN)      | 需要绑定到id列的值。                                           |
| bufLen (IN)     | 需要绑定到id列的列宽，该设置仅对变长类型生效。               |
| indicator (OUT) | 辅助指针，其值表示结果集取值情况。值为YAC_NULL_DATA表示取出的数据为NULL，其他值表示取出值的实际大小。<br/>当赋值为NULL时，表示该指针不生效。 |

目前输出数据缓冲区取数据不支持变长类型截断，取结果集数据时，所有变长类型遇到大小不足的输出数据缓冲区会报错。
