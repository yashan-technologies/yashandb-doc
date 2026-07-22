## 功能简介

OCIStringAssignText函数用于将文本字符串分配给字符串。

## 函数声明

```c
sword OCIStringAssignText ( OCIEnv           *env,
                            OCIError         *err, 
                            CONST OraText    *rhs, 
                            ub2              rhs_len,
                            OCIString        **lhs );
```

## 参数说明

|  参数名| 说明|
| ---------------- |------------------------------------------------------------------------|
| env(IN)      | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。                          |
| err (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| rhs (IN)       | 源文本字符串。                                                                |
| rhs_len (IN) | 源文本字符串的长度（单位：字节）。                                                      |
| lhs (IN/OUT)    | 目标字符串。                                                                 |
