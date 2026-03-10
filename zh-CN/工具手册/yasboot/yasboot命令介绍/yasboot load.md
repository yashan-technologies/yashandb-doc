## load

本命令用于拆分大的CSV文件，并将拆分后的小文件导入YashanDB的物理表中。本模块中，文件拆分和导入的功能都由yasldr提供。

|  选项| 含义|
| --------------------------- | ------------------------ |
| *-c, --cluster*             | YashanDB的集群名（必传参数）                                 |
| *-b, --batch-size*          | 每批次的CSV数据行数，范围[1,65535]                           |
| *-m, --mode*                | 导入模式，包括BASIC和BATCH模式                               |
| *-sm, --split-mode*         | 拆分模式，包括PART、NODE和NODEPART模式，介绍如下文，默认为PART。导入分布式复制表必须使用`PART`拆分模式|
| *-f, --control-file*        | `control_file`文件路径，该文件必须包含`run_level=SPLIT`或`run_level=SPLIT_to_part`   |
| *-j,--part-job*             | 导入分区数据到节点时并行启动的yasldr数量                   |
| *-n,--no-load*              | 只拆分不导入                                |
| *-s,--split-file-directory* | 已拆分文件路径，表示将已拆分好的数据导入数据库|
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password* | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码                         |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *-tl,--to-local*            | 将拆分好的文件打包到节点上并执行本地导入      |
| *--param*                   | 传递给yasldr的命令行参数                      |
| *--gz*                      | 压缩打包文件                   |
| *--delete*                  | 删除拆分文件存放目录中的CSV文件                      |
| *--host-id*                 | 拆分前CSV文件所在的服务器ID，可以通过`yasboot cluster status`命令查询，默认为当前服务器ID  |
| *-w, --nowait*              | 运行后不等待执行命令结果                           |
| *-d, --child*               | 展示任务以及子任务信息                            |
| *--disable*                 | 屏蔽任务进度条展示                              |

> **Note**:
>
> 拆分文件存放目录是通过`control_file`中的`directory_clause`指定，不指定时默认以infile的第一个文件所在的目录作为输出目录。`directory_clause`语句详细说明请参考[yasldr](../../yasldr/yasldr使用指导)。

**拆分模式**

`yasboot load`命令支持以下三种拆分模式将CSV拆分成分区数据：

|  拆分模式| 说明|
| -------- | ------------------------ |
| PART     | <br/>\* 单机部署：<br/> &nbsp; &nbsp;- 拆分和导入都是连接主库。* 存算一体分布式集群部署： <br/>  &nbsp;&nbsp; - 连接DN主节点拆分成分区数据。<br/> &nbsp; &nbsp;- 将分区数据通过连接CN节点导入。 |
| NODE     | 存算一体分布式集群部署：<br/> &nbsp; &nbsp;- 连接CN节点拆分成节点数据； <br/> &nbsp; &nbsp;- 连接DN主节点，将节点数据导入各自节点。 |
| NODEPART | 存算一体分布式集群部署：<br/> &nbsp; &nbsp;-  连接CN节点拆分成节点分区数据；<br/> &nbsp; &nbsp;- 连接DN主节点，将节点分区数据导入各自节点。 |

示例

```shell
# 一键拆分和导入CSV数据（没有指定--host-id参数，默认CSV文件在当前服务器上）
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm PART --param "BATCH_SIZE=2048 CONN_POOL_SIZE=10"

# 一键拆分和导入服务器host0002上的CSV数据
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART --host-id host0002

# 一键拆分，打包传输到节点机器，进行本地导入CSV数据
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -tl

# 一键拆分CSV文件
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -n

# 一键拆分CSV文件，并打包传输到节点
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -n -tl

# 一键导入已拆分好的CSV数据（前提：已完成了一键拆分CSV文件命令）
$ yasboot load -c yashandb -s /var/database/yashan/load/splitFileDirectory
```
