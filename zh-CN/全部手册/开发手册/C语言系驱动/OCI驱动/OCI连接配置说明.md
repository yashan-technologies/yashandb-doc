本文将介绍YashanDB的OCI连接配置方式。

## 使用URL连接

使用目标数据库的URL建立连接，支持单IP、多IP或多IP组，URL格式如下：

* 单IP： `host:port[/pdb_name]`。

* 多IP：`serverType:host:port,host:port,host:port,host:port[/pdb_name]`，多个地址间采用`,`分隔，连接时根据serverType配置对相应节点进行轮询连接。

* 多IP组：`serverType:host:port,host:port;host:port,host:port[/pdb_name]`，多个IP组间采用`;`分隔，连接时先在组内根据serverType配置对相应节点进行轮询连接，当组内所有连接均失败后按顺序优先级（越靠前优先级越高）访问下一组。

参数含义：

* host:port[/pdb_name]：服务端的监听地址，如需连接部署为容器数据库的YashanDB时，必须使用v23.5.1.100及以上版本的OCI驱动并指定正确的[目标容器名称](../../../数据库管理/租户管理/管理PDB/查看PDB信息)，省略则默认连接至根容器。

*  serverType：连接模式，可选项包括[primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance]。


## 使用别名连接

使用别名建立连接，需先在$YASDB_HOME/client/yasc_service.ini中定义指向目标数据库的别名。

```shell
vi yasc_service.ini

CITEST = 192.168.1.2:1688
REMOTE = PRIMARY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
REMOTE2 = STANDBY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD = LOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD2 = PRIMARYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD3 = STANDBYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
```
