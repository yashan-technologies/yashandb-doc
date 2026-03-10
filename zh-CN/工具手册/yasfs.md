yasfs是单实例的YFS服务程序，在共享集群安装部署、离线升级以及离线修改集群配置时提供文件系统服务。

>**Warn**:
>
> 仅允许在使用ycsctl离线修改集群配置时启动单实例yasfs服务，ycsctl离线操作结束后，必须先停止yasfs服务再启动集群。
>
> 单实例yasfs服务运行时，同一共享集群的其他节点不得启动YCS或yasfs服务，**否则会造成元数据损坏或数据丢失**。

## 查看帮助

使用yasfs -h可以查看帮助信息。

```shell
$ yasfs -h
yasfs: option requires an argument -- 'h'

Usage:
  yasfs [OPTION]

Options:
  -D  home path, use YASCS_HOME instead if this option is not given
  -u  upgrade mode
```

## yasfs启停

yasfs可以通过以下2种方式启动：

```shell
# 执行时，请将示例路径替换为实际路径
方式1：设置YASCS_HOME环境变量后，运行yasfs
$ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1; 
$ yasfs &

方式2：通过-D选项指定YASCS_HOME
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1 &
```

yasfs可以通过以下2种方式停止：

```bash
方式1：设置YASCS_HOME环境变量后，执行shutdown
$ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1
$ yfscmd exec "shutdown abort"

方式2：通过-D选项指定YASCS_HOME
$ yfscmd -D /data/yashan/yasdb_data/ycs/ce-1-1 exec "shutdown abort"
```
