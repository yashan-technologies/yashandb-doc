## 功能简介

OCIErrorGet函数用于返回缓冲区中的错误消息和YashanDB数据库错误代码。

## 函数声明

```c
sword OCIErrorGet ( void       *hndlp, 
                    ub4         recordno,
                    OraText    *sqlstate,
                    sb4        *errcodep, 
                    OraText    *bufp,
                    ub4         bufsiz,
                    ub4         type );
```

## 参数说明

|  参数名| 说明|
| -------------- | ---------------------------------------- |
| hndlp (IN)     | 错误句柄。                                 |
| recordno (IN)  | 指示应用程序从中查找信息的状态记录。       |
| sqlstate (OUT) | SQL状态。                                  |
| errcodep (OUT) | 返回的错误代码。                           |
| bufp (OUT)     | 返回的错误消息文本。                       |
| bufsiz (IN)    | 为错误消息提供的缓冲区大小（单位：B）。 |
| type (IN)      | 句柄类型。                                 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| -------- | --------------- |
| type     | OCI_HTYPE_ERROR |
| recordno | 1               |
