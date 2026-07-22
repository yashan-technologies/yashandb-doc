## host info

本命令用于展示当前服务器的环境信息。

|  选项| 含义|
| ------------- | -------------------------- |
| *-c,--cpu*    | 收集服务器的CPU信息          |
| *\-d, --disk* | 收集服务器的磁盘信息         |
| *\-m, --mem*  | 收集服务器的内存信息         |
| *\-n, --net*  | 收集服务器的网络信息         |
| *\-a, --all*  | 查看服务器以上选项的所有信息 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 服务器基本信息
$ yasboot host info
{
  "host": {
    "hostname": "localhost.localdomain",
    "uptime": 8676,
    "bootTime": 1678325128,
    "procs": 261,
    "os": "linux",
    "platform": "centos",
    "platformFamily": "rhel",
    "platformVersion": "7.6.1810",
    "kernelVersion": "3.10.0-957.el7.x86_64",
    "kernelArch": "x86_64",
    "virtualizationSystem": "",
    "virtualizationRole": "",
    "hostid": "e6800d7f-5399-4172-ac8a-661764ed5adc"
  }
}

# 其它用法，例如查看内存、磁盘等信息
$ yasboot host info -c -d -m -n

# 单次查看所有信息
$ yasboot host info -a
```

## host check

本命令用于对指定服务器进行检查是否满足数据库集群部署的环境要求。

|  选项| 含义|
| --------------- | ------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                            |
| *-host-id*      | 检查服务器的ID（必传参数） |
| *--ce-disk-path* | 共享集群磁阵路径（隐藏参数）        |
| *--install-path* | 服务器中YashanDB的安装路径（隐藏参数）           |
| *--version*      | 版本信息（隐藏参数）              |
| *-f, --force*    | 强制检查（隐藏参数）              |
| *--openssl*      | 检查openssl信息（隐藏参数）       |
| *--disk-found-path*      | 共享集群磁盘发现路径（隐藏参数）       |
| *--paths*      | 需要校验权限的路径（隐藏参数）       |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot host check -c yashandb --host-id host0001
```

## host add

本命令用于对新增的远程服务器执行软件包安装，软件包为tar.gz格式。

|  选项| 含义|
| ---------------- | ------------------------------------ |
| -c,--cluster     | 集群名称（必传参数）                             |
| -i,--install-pkg | 软件包文件本地绝对路径（废弃参数）                   |
| *--plugin*    | 插件包文件本地路径（废弃参数）        |
| -f, --force      | 忽略错误并强制安装，默认为false      |
| -t,--toml        | 要安装软件包的服务器相关信息的配置文件（必传参数） |
| *-d, --child*    | 展示任务以及子任务信息               |
| --disable        | 屏蔽任务进度条展示                   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *--deps* | 依赖包文件本地路径（隐藏参数）                 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot host add -c yashandb -t hosts_add.toml -d
```

## host cgroup create

本命令用于在单机部署（非级联备）和存算一体分布式集群部署的YashanDB环境中创建数据库资源管理cgroup目录。

|  选项| 含义|
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*        | 集群名称（必传参数）                                         |
| *--host-id*           | 服务器ID，可以通过`yasboot cluster status`命令查询，默认是部署数据库的所有服务器 |
| *-su,--sudo-username* | 具有sudo权限的SSH用户                                        |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码                                    |
| *--port*              | 服务器SSH端口，默认为22                                        |
| *--cgroup-path*       | 资源管理cgroup目录，默认为`/sys/fs/cgroup`                   |
| *--disable*           | 屏蔽任务进度输出                                             |
| *-d,--child*          | 展示包含子任务执行信息                                       |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 为环境中所有服务器创建资源管理cgroup目录
$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ******

# 创建host0002的资源管理cgroup目录
$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host0002
```

> **Note**:
>
> 如果执行该命令前，已经创建了资源使用组或资源计划指令，需要重启数据库生效。

## host cgroup remove

本命令用于移除数据库资源管理cgroup目录，成功执行完该命令后，需要重启服务器生效。

仅适用于单机部署（非级联备）和存算一体分布式集群部署的YashanDB环境。

|  选项| 含义|
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*        | 集群名称（必传参数）                                         |
| *--host-id*           | 服务器ID，可以通过`yasboot cluster status`命令查询，默认是部署数据库的所有服务器 |
| *-su,--sudo-username* | 具有sudo权限的SSH用户                                        |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码                                    |
| *--port*              | 服务器SSH端口，默认为22                                        |
| *--disable*           | 屏蔽任务进度输出                                             |
| *-d,--child*          | 展示包含子任务执行信息                                       |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 为环境中所有服务器移除资源管理cgroup目录
$ yasboot host cgroup remove -c yashandb --sudo-username root --sudo-password ******

# 移除host0002的资源管理cgroup目录
$ yasboot host cgroup remove -c yashandb --sudo-username root --sudo-password ****** --host-id host0002
```

## host auth add

本命令用于为当前用户开通操作系统认证。

|  选项| 含义|
| ---------------- | ------------------------------------ |
| *-c, --cluster*  | 集群名称（必传参数）                             |
| *-su, --sudo-username* | 具有sudo权限的SSH用户名（用于执行需要sudo权限的命令，例如创建YASDBA组，将用户加入YASDBA组） |
| *-sp, --sudo-password* | 具有sudo权限的SSH用户密码 |
| *-N, --nopassword* | SSH免密登录 |
| *--port* | 服务器SSH连接端口 |
| *--host-ids*     | 已部署服务器的ID，多台服务器使用逗号分隔       |
| *-w, --nowait*  | 运行后不等待执行命令结果           |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽任务进度条展示                 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot host auth add -c yashandb -su yashan -sp password
```

## host auth remove

本命令用于在已部署的所有服务器上移除操作系统用户的免密登录。

|  选项| 含义|
| ---------------------- | ------------------------- |
| *-c, --cluster*        | 集群名称（必传参数）      |
| *-su, --sudo-username* | 具有sudo权限的ssh用户名   |
| *-sp, --sudo-password* | 具有sudo权限的ssh用户密码 |
| *-N, --nopassword*     | SSH免密登录               |
| *--port*               | 服务器SSH连接端口           |
| *--host-ids*    | 已部署服务器的ID，多台服务器使用逗号分隔       |
| *-w, --nowait*  | 运行后不等待执行命令结果           |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽任务进度条展示                 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot host auth remove -c yashandb -su yashan -sp password
```

## host remove

本命令用于卸载闲置的远程服务器上已安装的YashanDB服务端。

|  选项| 含义|
| ---------------- | ------------------------------------ |
| *-c,--cluster*  | 集群名称（必传参数）                             |
| *--host-ids*    | 已部署服务器的ID，多台服务器使用逗号分隔       |
| *--ip*          | 已部署服务器的IP，多台服务器使用逗号分隔       |
| *-t, --toml*    | 以SSH方式卸载需要指定服务器配置文件`hosts.toml`的路径         |
| *-f,--force*    | 强制卸载标志，不使用此参数时提示二次确认             |
| *-w, --nowait*  | 运行后不等待执行命令结果           |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽任务进度条展示                 |
| *--with-unconnected-host* | 如果服务器已经无法连接，使用该参数强制删除服务器。仅删除服务器在yasom的数据，yasagent进程和安装包都将保留 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot host remove -c yashandb --host-ids host0002
```
