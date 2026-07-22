YCS提供运行日志用于所在服务器的监控和分析，日志文件位于YCS的HOME目录下的/log/run文件夹中，路径及名称不可修改。

运行日志通过日志级别控制记录开关和记录策略，级别为OFF时不记录运行日志。

## 设置日志文件保留个数

通过设置LOG_NUMBER参数修改运行日志的保留个数，当达到该参数设置的值（默认10）时，系统会自动删除时间靠前的日志。

取值范围/格式：[2,10000]

日志文件保留个数修改指令：

```shell
$ ycsctl set log_number 10
```

查看当前日志文件保留个数的命令如下：

```shell
$ ycsctl get log_number
YCS LOG_NUMBER = 10
```

可进入日志文件存放位置观察文件的保留数量：

```shell
$ ls -l $YASCS_HOME/log/run
total 1168
-rw-r----- 1 yashan yashan 1193430 Jul 19 10:09 run.log
```

## 设置日志文件最大大小

通过设置LOG_SIZE参数修改运行日志的最大大小，当达到该参数设置的值（默认20M）时，该日志文件会自动归档。

取值范围/格式：[1M,4G]

日志的最大大小修改指令：

```shell
$ ycsctl set log_size 20M
```

查看当前日志的最大大小的命令如下：

```shell
$ ycsctl get log_size
YCS LOG_SIZE = 20MB
```

## 运行日志级别管理 

运行级别为OFF时不记录运行日志。

其他运行日志级别的排序为：

ERROR < WARN < INFO < DEBUG < TRACE < ALL

从左到右，日志等级依次增高，高日志级别包含低日志级别。

查看当前运行日志级别的命令如下：

```shell
$ ycsctl get log_level
YCS LOG_LEVEL = DEBUG
```

通过修改LOG_LEVEL参数（重启生效），或者执行如下命令（立即生效）修改运行日志级别：

```shell
$ ycsctl set log_level info

$ ycsctl get log_level
YCS LOG_LEVEL = info
```

### ERROR级别

共享集群/分布式集群实例产生了可处理的关键错误时，将输出ERROR日志。这些错误一般无法返回给客户端，例如后台线程的产生的错误。

将日志级别设置为ERROR的指令如下：

```shell
ycsctl set LOG_LEVEL "ERROR"
```

示例

```verilog
//当YCS因为服务器断连，处理消息失败时，run.log打印情况如下：
2023-07-05 18:49:49.331 4334 [ERROR][errno=00406]: [YCS] failed to process yascs client message: connection is closed
```

### INFO级别

共享集群/分布式集群实例默认使用的日志级别为INFO。

INFO级别记录共享集群/分布式集群实例正常运行中发生的关键事件，主要包括：

- 共享集群/分布式集群实例状态变更：例如启动、关闭、升主降备。
- 共享集群执行工具运维操作：例如工具的启动和结束。
- 共享集群/分布式集群关键活动：例如拓扑状态变化等。
- 共享集群/分布式集群参数变化：修改系统配置参数等。

将日志级别设置为INFO的指令如下：

```shell
ycsctl set LOG_LEVEL "INFO"
```

示例

```verilog
//共享集群拓扑状态变化，run.log打印情况如下：
2023-06-27 15:54:41.207 129728 [INFO] Topo ver:1, config ver:6, age:163, self node id:0, cluster master id:0, yasfs master id:255, yasdb master id:255, active node count:1
2023-06-27 15:54:41.207 129728 [INFO] Node 0: online  yasfs: offline yasdb: offline  yasdb inter url:
2023-06-27 15:54:41.207 129728 [INFO] Node 1: offline yasfs: offline yasdb: offline  yasdb inter url:
2023-06-27 15:54:41.207 129728 [INFO] Node 2: offline yasfs: offline yasdb: offline  yasdb inter url:
2023-06-27 15:54:41.207 129728 [INFO] Node 3: offline yasfs: offline yasdb: offline  yasdb inter url:
2023-06-27 15:54:41.208 129728 [INFO] begin to start all resources
2023-06-27 15:54:41.208 129728 [INFO] Topo ver:2, config ver:6, age:163, self node id:0, cluster master id:0, yasfs master id:0, yasdb master id:0, active node count:1
2023-06-27 15:54:41.208 129728 [INFO] Node 0: online  yasfs: offline yasdb: offline  yasdb inter url:
2023-06-27 15:54:41.208 129728 [INFO] Node 1: offline yasfs: offline yasdb: offline  yasdb inter url: 
2023-06-27 15:54:41.209 129728 [INFO] Node 2: offline yasfs: offline yasdb: offline  yasdb inter url: 
2023-06-27 15:54:41.209 129728 [INFO] Node 3: offline yasfs: offline yasdb: offline  yasdb inter url: 
```

### DEBUG级别

DEBUG级别用于打印问题追溯时可能用到的关键信息。

将日志级别设置为DEBUG的指令如下：

```shell
ycsctl set LOG_LEVEL "DEBUG"
```

示例

```verilog
//当最后一个共享集群实例停止时，放弃switch over，run.log打印如下：
2023-06-30 15:21:07.028 91394 [DEBUG] Self is last node, quit without switch over
2023-06-30 15:21:07.028 91394 [DEBUG] Stop inspect, exception triggered will not be processed any more.
2023-06-30 15:21:07.028 91394 [DEBUG] Try close all channels
```

###  ALL级别

ALL级别记录所有日志信息，是最高级别的日志级别模式。

将日志级别设置为ALL的指令如下：

```shell
ycsctl set LOG_LEVEL "ALL"
```
