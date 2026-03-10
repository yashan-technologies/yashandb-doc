## 功能简介

OCIBindByName函数用于将程序变量绑定到SQL语句或PL块中的占位符，变量个数应不小于SQL语句或PL块中的占位符个数，变量名称应为SQL语句或PL块中存在的占位符名称。


## 函数声明

```c
sword OCIBindByName (
              OCIStmt       *stmtp, 
              OCIBind       **bindp,
              OCIError      *errhp,
              const OraText    *placeholder,
              sb4           placeh_len,
              void         *valuep,
              sb4           value_sz,
              ub2           dty,
              void         *indp,
              ub2           *alenp,
              ub2           *rcodep,
              ub4           maxarr_len,
              ub4           *curelep, 
              ub4           mode ); 
```

## 参数说明

| 参数名                | 说明                                                                                                         |
|--------------------|------------------------------------------------------------------------------------------------------------|
| stmtp (IN/OUT)     | 正在处理的SQL或PL语句的语句句柄。                                                                                        |
| bindpp (IN/OUT)    | 此调用隐式分配的绑定句柄的地址。绑定句柄维护该特定输入值的所有绑定信息。当语句句柄被释放时，该句柄被隐式释放。                                                    |
| errhp (IN/OUT)     | 一个错误句柄，当出现错误时可以获取诊断信息。                                                                                     |
| placeholder (IN)   | 由名称指定的占位符属性。                                                                                               |
| placeh_len (IN)    | 占位符名称长度。                                                                                                   |
| valuep (IN/OUT)    | 数据值的地址或dty参数中指定类型的数据值数组。<br />可以指定一个数据值数组，用于映射到PL表或为SQL多行操作提供数据。对于LOB，必须是指向OCILobLocator类型的LOB定位器的指针。 |
| value_sz (IN)      | 此绑定变量中任何数据值的可能最大规格，使用valuep传递，单位为字节。<br />在数组绑定的情况下，该参数为任何元素的可能最大规格，实际规格在alenp参数中指定。                       |
| dty (IN)           | 要绑定的值的数据类型。                                                                                                |
| indp (IN/OUT)      | 指向指示器变量或数组的指针。                                                                                             |
| alenp (IN/OUT)     | 指向数组元素实际长度的数组的指针。                                                                                          |
| rcodep (OUT)       | 指向列级返回代码数组的指针。<br />动态绑定时，忽略此参数。                                                                           |
| maxarr_len (IN)    | 用户数组可以容纳的元素的最大可能数量。<br />仅用于PL索引表绑定。                                                                       |
| curelep (IN/OUT)   | 当前数组长度参数，指向执行操作之前或之后数组中实际元素数的指针。<br />仅用于PL索引表绑定。                                                          |
| mode (IN)          | 支持的有效模式。                                                                                                   |

部分参数目前仅支持以下取值：

| 参数       | 有效值                                                                                                                                      |
| ---------- |------------------------------------------------------------------------------------------------------------------------------------------|
| dty (IN)   | SQLT_INT<br />SQLT_STR<br />SQLT_AFC<br />SQLT_LNG<br />SQLT_FLT<br />SQLT_TIMESTAMP<br />SQLT_INTERVAL_DS<br />SQLT_VNU<br />SQLT_BDOUBLE<br />SQLT_BLOB<br />SQLT_CLOB<br />SQLT_RSET |
| rcodep     | NULL                                                                                                                                     |
| *bindpp    | NULL                                                                                                                                     |
| maxarr_len | 0                                                                                                                                        |
| curelep    | NULL                                                                                                                                     |
| mode       | OCI_DEFAULT<br />OCI_DATA_AT_EXEC                                                                                                        |
