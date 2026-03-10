## yasboot init

本命令提供交互式的快速部署单机数据库的方式。

|  选项| 含义|
| ------------------- | ------------------------------------------------------------ |
| --mode              | 安装模式，可选[local-host, multi-host]                       |
| --ip                | 部署的IP地址，仅支持IPv4地址，支持如`192.168.1.2,192.168.1.3`或者`192.168.1.[2-4]`两种格式 |
| -u, --username      | 服务器SSH用户名                                              |
| -p, --password      | 服务器SSH登录密码                                            |
| -N,--no-password    | SSH免密登录                                                  |
| --ssh-port          | 服务器SSH连接端口                                            |
| -c, --cluster       | 集群名称                                                     |
| --node              | 单机数据库的节点规模                                         |
| --plugins           | 需要安装的插件包，可选[all,s3,gis,dblink,udf,listagg,none]，默认为all，支持选择多个，使用逗号分隔 |
| --install-path      | 安装路径，生成的YASDB_HOME为\<install-path\>/\<version\>     |
| --data-path         | 数据路径，生成的YASDB_DATA为\<data-path\>/db-\<nodeid\>      |
| --log-path          | yasagent、yasom、数据库run.log、slow.log的路径               |
| --listen-on         | yasdb进程监听的地址，可选值[mange-ip, 0.0.0.0]               |
| -sp, --sys-password | 设置数据库超级管理员sys用户的密码，配置要求如下：<br/>* 密码长度为8 - 64位<br/>* 密码中不能包含对应的数据库用户名称<br/>* 密码必须同时包含数字、字母和特殊字符<br/>* OS命令相关的特殊字符（例如`@`、`/`、`.`、`!`、`$`、`'`等）需进行转义                                                |
| --begin-port        | 起始端口                                                     |
| --memory-limit      | 服务器的可使用内存百分比上限，0表示不设限                    |
| --config-mode       | 配置模式，可选值[config-only,install-now]                  |
| --fail-opt          | 失败后的操作，可选值[save,clean]                            |
| --env-opt           | 部署成功后环境变量的添加方式，可选值[automatic,manual]         |
| --monit-opt         | 部署成功后monit的打开方式，可选值[automatic,manual]           |
| -y, --yes           | 所有询问[y/n]的地方都选择yes                                 |
| --deps             | 依赖包文件本地路径（隐藏参数）                 |
| -t, --yas-type      | 数据库部署的部署形态：可选值[SE, CE]            |
| --ce-data           | 共享集群数据盘，支持输入多个，使用逗号分隔        |
| --disk-found-path   | 共享集群磁盘发现路径，支持输入多个，使用逗号分隔                                  |
| --system-data       | 共享集群系统数据盘，支持输入多个，使用逗号分隔                                   |
| --group             | 共享集群组的个数                                |
| --standby-node      | 共享集群类型备集群节点的部署规模                 |
| -fg,--failgroup     | 共享集群磁盘组的故障组数量                       |
| --yfs-force-create  | YFS强制创建diskgroup（仅适用共享集群）           |
| --ignore-hostname   | 忽略服务器名（隐藏参数）                |

> **Note**:
>
> 指定--ignore-hostname时，yasboot将忽略服务器名称直接生成默认字符串（yas1，yas2……）作为集群节点名进行部署，**不推荐使用**，以服务器名称作为集群节点名更便于管理。

示例

```shell
# 推荐：执行如下命令进入交互式部署
$ ./bin/yasboot init 

# 命令行，忽略检查失败项可能会造成部署失败
$ ./bin/yasboot init --mode single-host --ip 127.0.0.1 -u yashan -p password --ssh-port 22 -c yashandb --node 1 --plugins all --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data --log-path /data/yashan/log --listen-on manage-ip --sys-password password --begin-port 1688 --memory-limit 80 -y --yas-type SE
```
