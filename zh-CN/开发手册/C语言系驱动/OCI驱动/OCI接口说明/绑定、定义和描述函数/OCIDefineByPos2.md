## 功能简介

OCIDefineByPos2函数是OCIDefineByPos函数的扩展版本，用于将选择列表中的项目与类型和输出数据缓冲区相关联。OCIDefineByPos2与OCIDefineByPos的主要区别在于支持更大的缓冲区大小（通过ub4类型的value_sz参数替代sb4类型）。

## 函数声明

```c
sword OCIDefineByPos2 ( OCIStmt     *stmtp,
                        OCIDefine  **defnpp,
                        OCIError    *errhp,
                        ub4          position,
                        void        *valuep,
                        ub4          value_sz,
                        ub2          dty,
                        void        *indp,
                        ub2         *rlenp,
                        ub2         *rcodep,
                        ub4          mode );
```

## 参数说明

|  参数名| 说明|
| --------------- | ----------------------------------------------- |
| stmtp (IN/OUT)  | 请求的SQL查询操作的句柄。                         |
| defnpp (IN/OUT) | 指向定义句柄的指针。                        |
| errhp (IN/OUT)  | 一个错误句柄，当出现错误时可以获取诊断信息。      |
| position (IN)   | 此值在选择列表中的位置。                          |
| valuep (IN/OUT) | 指向dty参数中指定类型的缓冲区或缓冲区数组的指针。 |
| value_sz (IN)   | 每个valuep缓冲区的大小（单位：B）。与OCIDefineByPos不同，此参数类型为ub4，支持更大的缓冲区大小。         |
| dty (IN)        | 数据类型。                                        |
| indp (IN)       | 指向指示器变量或数组的指针。                      |
| rlenp (IN/OUT)  | 指向获取的数据长度数组的指针。                    |
| rcodep (OUT)    | 指向列级返回代码数组的指针。                      |
| mode (IN)       | 支持的有效模式。                                  |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| -------- |----------------------------------------------------------------------------------------------------------------------------|
| dty (IN) | SQLT_INT<br />SQLT_STR<br />SQLT_AFC<br />SQLT_CHR<br />SQLT_FLT<br />SQLT_TIMESTAMP<br />SQLT_INTERVAL_DS<br />SQLT_BLOB<br />SQLT_CLOB<br />SQLT_RSET |
| mode     | OCI_DEFAULT<br />OCI_DYNAMIC_FETCH                                                                                                       |
| defnpp   | NULL                                                                                                                       |
| position | \>= 1                                                                                                                      |

目前输出数据缓冲区取数据不支持变长类型截断，取结果集数据时，所有变长类型遇到大小不足的输出数据缓冲区会报错。