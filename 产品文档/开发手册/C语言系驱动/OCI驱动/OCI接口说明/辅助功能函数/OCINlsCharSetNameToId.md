## 功能简介

OCINlsCharSetNameToId函数用于返回字符集名称对应的ID。

## 函数声明

```c
ub2 OCINlsCharSetNameToId ( void            *hndl, 
                            const OraText   *name );
```

## 参数说明

| 参数名       | 说明       |
| ------------ | ---------- |
| hndlp (IN)   | 环境句柄。   |
| name(IN/OUT) | 字符集名称。 |

部分参数目前仅支持以下取值：

| 参数 | 有效值                   |
| ---- | ------------------------ |
| name | GBK<br />UTF8<br />UTF16 |
