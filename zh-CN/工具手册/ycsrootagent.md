ycsrootagent是YCS的特权操作代理，主要以后台进程的方式运行并为YCS提供服务，支持YCSRA服务启停、SCSI I/O Fencing和磁盘I/O测试等命令行接口。

>**Note**:
>
> ycsrootagent提供的所有接口都需要在root下（或sudo）执行。

## 查看帮助

使用ycsrootagent -h命令可查看帮助信息。

```shell
$ ycsrootagent -h
```

## ycsrootagent {start|stop}

本命令用于手动启/停YCSRA后台服务进程。YCSRA进程将为YCS提供关键服务，为保障集群的正常运行，不得轻易手动停止。

若通过yasboot部署集群，其会在用户提供了sudo用户名密码或配置了sudo免密时自动启动YCSRA。若YCSRA进程异常，请按示例手动启动YCSRA以恢复服务。

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-H* | YCSRA运行的home目录，即进程所服务的YCS的home目录，必须为绝对路径，不可省略 |

示例：以home目录是/data/yashan/yasdb_data/ycs/ce-1-1为例，实际操作时请替换成真实的路径。

```shell
$ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
YCSRA instance start successfully.

$ sudo ycsrootagent stop -H /data/yashan/yasdb_data/ycs/ce-1-1

# 权限不足的错误示例
$ ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
YAS-00521 process permission is insufficient.
```

<span id="scsi_clear" name="scsi_clear"></span>

## ycsrootagent scsi {allow|ban|clear}

在需要自动化检测存储设备是否支持SCSI I/O Fencing的场景中，yasboot会自行调用fenceScsiCheck脚本从而调用执行本命令的相关子命令。如需手动进行相应检测，请直接执行[fenceScsiCheck脚本](../数据库管理/集群管理/IO Fencing/基于SCSI持久预留的IO Fencing.md#fenceScsiCheck_usage)，**不建议用户直接执行本命令**。

clear子命令用于清理存储设备上残留的持久预留信息，仅在收到明确的清理提示或YAS-00341错误码时可**按需谨慎执行**。

>**Warn**:
>
> 本命令会改变存储设备的访问规则和可访问性，错误用法将严重影响集群的运行。

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-d* | 操作的单个磁盘设备名，必须为绝对路径，不可省略。最长31个字符 |
| *-a* | 若带此选项，表明命令正在辅助测试的服务器上执行 |
| *-f* | 仅对clear子命令有效，若带此选项，将跳过风险提示并强制执行 |

## ycsrootagent pingdisk

在需要自动化检测存储设备是否支持SCSI I/O Fencing的场景中，yasboot会自行调用fenceScsiCheck脚本从而调用执行本命令。如需手动进行相应检测，请直接执行[fenceScsiCheck脚本](../数据库管理/集群管理/IO Fencing/基于SCSI持久预留的IO Fencing.md#fenceScsiCheck_usage)，**不建议用户直接执行本命令**。

>**Warn**:
>
> 本命令会多次读写设备的特定区域，错误用法将影响性能。

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-d* | 操作的单个磁盘设备名，必须为绝对路径，不可省略。最长31个字符 |
