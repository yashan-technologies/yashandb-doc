## 功能简介

OCIStringSize函数用于获取字符串大小。

## 函数声明

```c
ub4 OCIStringSize ( OCIEnv              *env,
                    CONST OCIString     *vs );
```

## 参数说明

| 参数名           | 说明                                            |
| ---------------- |-----------------------------------------------|
| env(IN)      | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。 |
| vs (IN)      | 字符串指针。                                        |
