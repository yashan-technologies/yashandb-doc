ycsctl是YashanDB的YCS管理工具，用户可使用本工具实现对共享集群/分布式集群的管理操作，包括集群级别的管理和节点级别的管理。

- [集群管理命令](集群管理命令)：要求YFS服务启动后才能执行。

- [节点管理命令](节点管理命令)：要求节点上的YCS启动后才能执行。

在使用ycsctl前，必须先在每台服务器上正确配置$YASCS_HOME环境变量：

 ```shell
 # 如下路径需更换为实际的节点路径
 # 实例1-1
 $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1

 # 实例1-2
 $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-2
 ```
