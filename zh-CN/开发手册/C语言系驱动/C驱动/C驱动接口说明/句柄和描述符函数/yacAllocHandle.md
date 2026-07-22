## 功能简介

yacAllocHandle函数用于为句柄分配空间，返回YAC_SUCCESS表示分配成功，返回YAC_ERROR表示分配失败。

申请空间分配的顺序需遵照[句柄的层级](00句柄和描述符函数)，上一层级句柄作为本句柄申请空间时的输入句柄。

## 函数声明

```c
yacResult yacAllocHandle ( yacHandleType type,
                           yacHandle     input,
                           yacHandle*    output);
```

## 参数说明

|  参数名| 说明|
| ------------ | -------------------------------- |
| type (IN)    | 句柄类型。包含如下值：<br>YAC_HANDLE_ENV：全局环境句柄<br>YAC_HANDLE_DBC：数据库连接句柄<br>YAC_HANDLE_STMT：SQL语句句柄 |
| input (IN)   | 输入句柄。type为YAC_HANDLE_ENV时，本参数输入NULL。 |
| output (OUT) | 输出句柄，该句柄挂在输入句柄上。 |
