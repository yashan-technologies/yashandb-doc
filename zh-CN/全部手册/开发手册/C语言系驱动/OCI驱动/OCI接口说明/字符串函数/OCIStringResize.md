## 功能简介

OCIStringResize函数用于调整字符串内存大小。

## 函数声明

```c
sword OCIStringResize ( OCIEnv          *env,
                        OCIError        *err,
                        ub4             new_size,
                        OCIString       **str );
```

## 参数说明

|  参数名| 说明|
| ---------------- |-------------------------------------------------------------------|
| env(IN)      | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。                  |
| err (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| new_size (IN)       | 字符串的新内存大小（单位：字节）。new_size必须包含NULL字符的空间作为字符串终止符。                   |
| str (IN/OUT)    | 要从OCI对象缓存中释放的字符串。                                                 |
