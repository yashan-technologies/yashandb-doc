## 功能简介

yacConnect函数用于客户端发起连接请求，返回YAC_SUCCESS表示连接成功，返回YAC_ERROR表示连接失败。

## 函数声明

```c
yacResult yacConnect ( yacHandle hConn,
                       const yacChar* url,
                      YacInt16 urlLength,
                      const yacChar* user,
                      YacInt16 userLength,
                      const yacChar* password,
                      YacInt16 passwordLength);
```

## 参数说明

|  参数名| 说明|
| ------------------- |---------------------------------------------------------|
| hConn (IN/OUT)      | 连接信息句柄。                   |
| url (IN)            | 目标数据库的URL，支持单IP、多IP或多IP组。<br/>配置格式如下：<br>\* 单IP： `host:port`<br>\* 多IP：`serverType:host:port,host:port,host:port,host:port`，多个地址间采用`,`分隔，连接时根据serverType配置对相应节点进行轮询连接。<br>\* 多IP组：`serverType:host:port,host:port;host:port,host:port`，多个IP组间采用`;`分隔，连接时先在组内根据serverType配置对相应节点进行轮询连接，当组内所有连接均失败后按顺序优先级（越靠前优先级越高）访问下一组。<br>参数含义：<br>\* host:port：服务端的监听地址。<br>\* serverType：连接模式，可选项包括[primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance]，详细介绍请查阅下文。 |
| urlLength (IN)      | 服务端地址长度，可传入YAC_NULL_TERM_STR表示以\0结尾。                     |
| user (IN)           | 用户名。                                  |
| userLength (IN)     | 用户名长度，可传入YAC_NULL_TERM_STR表示以\0结尾。             |
| password (IN)       | 用户密码。              |
| passwordLength (IN) | 用户密码长度，可传入YAC_NULL_TERM_STR表示以\0结尾。           |

监听地址中serverType参数的详细介绍如下表：

|  参数名| 说明|
| ------------------- |---------------------------------------------------------|
| primary |表示多IP下轮询连接高可用部署中的主库（不连接备库）。多IP/多IP组场景的默认模式，可省略。|
| standby |  表示多IP下轮询连接高可用部署中的备库（不连接主库）。|
| loadBalance |  表示多IP下会负载均衡地连接最优节点（即连接个数最少的节点）。|
| primaryLoadBalance |  表示多IP下会负载均衡地连接最优主库（连接个数最少的主库）。|
| standbyLoadBalance |  表示多IP下会负载均衡地连接最优备库（连接个数最少的备库）。 |
