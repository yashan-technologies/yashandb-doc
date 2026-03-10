命令参数
-------

参数格式：

```shell
ycsctl -H
ycsctl cmd [cmd_args]
```

**-H**

显示帮助信息。

**cmd**

ycsctl工具的子命令。cmd_args为子命令的参数。

子命令包括[集群管理命令](./集群管理命令)和[节点管理命令](./节点管理命令)。

其中，集群管理命令提供了一系列集群配置命令，这些配置信息都将被存储在YCR盘中，可通过ycsctl show config进行查看。
