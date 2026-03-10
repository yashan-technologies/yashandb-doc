## 功能简介

OCILobIsEqual函数用于判断两个LOB是否相等。

## 函数声明

```c
sword OCILobIsEqual ( OCIEnv                  *envhp,
                      const OCILobLocator     *x,
                      const OCILobLocator     *y,
                      boolean                 *is_equal );
```

## 参数说明

|  参数名| 说明|
| ------ |--------------------------------------------|
| envhp (IN) | 环境句柄。                                    |
| x (IN) | 唯一引用LOB的内部LOB定位器。 |
| y (IN) | 唯一引用LOB的内部LOB定位器。 |
| is_equal (OUT) | 如果两个LOB相等返回TRUE，否则返回FALSE。                 |
