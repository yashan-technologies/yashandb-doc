## 功能简介

OCIServerDetach函数用于删除数据源访问路径。

## 函数声明

```c
sword OCIServerDetach ( OCIServer   *srvhp,
                        OCIError    *errhp,
                        ub4          mode ); 
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| srvhp (IN)     | 初始化的服务器上下文的句柄。<br />该上下文被重置为未初始化状态，句柄未解除分配。 |
| errhp (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| mode (IN)      | 指定操作模式。                                                 |

部分参数目前仅支持以下取值：

| 参数   | 有效值         |
|------|-------------|
| mode | OCI_DEFAULT |