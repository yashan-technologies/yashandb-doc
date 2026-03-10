## 功能简介

yacGetEnvAttr函数用于获取环境句柄中的某个属性值，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

env级别的参数会对当前环境信息句柄下的所有句柄的行为产生影响，请通过[yacSetEnvAttr](yacSetEnvAttr)函数进行相关配置。

## 函数声明

```c
YacResult yacGetEnvAttr(YacHandle hEnv,
                        YacEnvAttr attr,
                        YacVoid* value,
                        YacInt32 bufLength,
                        YacInt32* stringLength);
```

## 参数说明

|  参数名| 说明|
| ------------------ | ---------------------------- |
| hEnv (IN/OUT)      | 环境句柄。                   |
| attr (IN)          | 属性类型。                   |
| value (OUT)        | 存储获取到的属性值。         |
| bufLength (OUT)    | 获取到的属性值的缓冲区长度。 |
| stringLength (OUT) | 获取到的属性值的实际长度。   |
