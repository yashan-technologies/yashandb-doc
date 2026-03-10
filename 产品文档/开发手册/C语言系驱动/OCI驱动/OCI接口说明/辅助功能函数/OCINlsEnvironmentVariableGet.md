## 功能简介

OCINlsEnvironmentVariableGet函数用于返回字符集ID。

## 函数声明

```c
sword OCINlsEnvironmentVariableGet ( void       *val,
                                     size_t      size,
                                     ub2         item,
                                     ub2         charset, 
                                     size_t     *rsize );
```

## 参数说明

| 参数名      | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| val(IN/OUT) | 字符集ID。                                                     |
| size(IN)    | 字符集ID的长度。                                               |
| item(IN)    | 获取的字符集类型：<br />OCI_NLS_CHARSET_ID：字符集。<br />OCI_NLS_NCHARSET_ID：国家字符集。 |
| charset(IN) | 配置名称的字符集。                                             |
| rsize(OUT)  | 字符集长度。                                                   |

部分参数目前仅支持以下取值：

| 参数   | 有效值                                      |
| ------ |------------------------------------------|
| item | OCI_NLS_CHARSET_ID<br />OCI_NLS_NCHARSET_ID  |