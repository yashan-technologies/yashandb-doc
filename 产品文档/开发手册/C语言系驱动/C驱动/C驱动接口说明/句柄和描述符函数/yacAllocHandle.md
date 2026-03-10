## 功能简介

yacAllocHandle函数用于为句柄分配空间，返回YAC_SUCCESS表示分配成功，返回YAC_ERROR表示分配失败。

## 函数声明

```c
yacResult yacAllocHandle ( yacHandleType type,
                           yacHandle     input,
                           yacHandle*    output);
```

## 参数说明

| 参数名       | 说明                             |
| ------------ | -------------------------------- |
| type (IN)    | 句柄类型。                       |
| input (IN)   | 输入句柄。                       |
| output (OUT) | 输出句柄，该句柄挂在输入句柄上。 |