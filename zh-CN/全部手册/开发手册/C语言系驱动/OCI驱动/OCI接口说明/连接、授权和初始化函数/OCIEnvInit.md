## 功能简介

OCIEnvInit函数用于分配并初始化环境句柄。

## 函数声明

```c
sword OCIEnvInit ( OCIEnv    **envhpp,
                   ub4         mode,
                   size_t      xtramemsz,
                   void      **usrmempp );
```

## 参数说明

|  参数名| 说明|
| -------------- | ---------------------------------------------------------- |
| envhpp (OUT)   | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。 |
| mode (IN)      | 指定模式的初始化。                                         |
| xtramemsz (IN) | 指定在环境持续时间内要分配的用户内存量。                     |
| usrmempp (OUT) | 返回一个指针，指向由调用为用户分配的xtramemsz大小的用户内存。 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ---- | ------------------------------------------ |
| mode | OCI_DEFAULT（默认值）<br /> OCI_ENV_NO_UCB |
