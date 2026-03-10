## collection host

本命令用于执行服务器基本信息的检查。

|  选项| 含义|
| -------------- | ------------------------------------------------------------ |
| *-c,--cluster* | YashanDB的集群名                                             |
| *-F,--format* | 服务器检查的结果格式化，可选json和html                         |
| *-o,--output* | 服务器检查结果和报告的输出目录                                 |
| *-n,--name*   | 服务器检查结果和报告的标识名称                                 |
| *-t,--toml*   | 使用toml文件内的配置项，格式请查阅[服务器检查配置文件](../配置文件/服务器检查配置文件)     |
| *--host*       | 若指定集群参数，则可为hostid，若不指定，则默认为服务器配置文件hosts.toml |
| *--disable*    | 屏蔽任务进度输出                                             |
| *-d,--child*   | 展示包含子任务执行信息                                       |

示例

```shell
# 执行本机的服务器检查
$ yasboot collection host

# 使用toml文件的配置项进行检查
$ yasboot collection host -t yascheck.toml

# 执行本机的服务器检查并且生成前端报告输出到规定目录
$ yasboot collection host -F html --output /home/yashan/check

# 执行agent服务器检查
$ yasboot collection host -c yashan

# 检查agent的目标服务器
$ yasboot collection host -c yashandb --host host0001

# 检查agent，并生成前端报告到目标目录，命名标识符号为yashan
$ yasboot collection host -c yashandb -t yascheck.toml -n yashan -o /home/yashan/check -F html

# 使用hosts.toml文件内的服务器执行SSH进行检查
$ yasboot collection host -t yascheck.toml --host hosts.toml
$ yasboot collection host --host hosts.toml

# 使用hosts.toml文件内的服务器执行SSH进行检查，并生成报告的目标目录
$ yasboot collection host -t yascheck.toml --host hosts.toml -n yashan -o /home/yashan/check -F html
```

<span id="CheckItems" name="CheckItems" class="yaslink"></span>

### 检查项

服务器检查分为6个模块，硬件检查，服务检查，系统参数检查，资源消耗，网络IO测试以及磁盘IO测试。

可以根据[服务器检查配置文件](../配置文件/服务器检查配置文件)指定检查项是否开启，然后执行命令时指定配置文件。

**硬件检查**

|  检查项| 检查内容|
| -------------- | ---------------------------------- |
| CPU            | 名称（Name）                       |
|                | 架构（Arch）                       |
|                | 物理CPU核数（PhysicalCPU）         |
|                | 逻辑CPU核数（LogicalCPU）          |
|                | 每个CPU拥有的核心数（CoresPerCPU） |
|                | 总核心数（ToltalCores）            |
| 磁盘（Disk）   | 设备名称（Device）                 |
|                | 文件系统（Filesystem）             |
|                | 大小（Size）                       |
|                | 已使用（Used）                     |
|                | 可用（Available）                  |
|                | 使用率（UsedPercent）              |
|                | 挂载路径（MountedOn）              |
| 网络（Net）    | 网络适配器（NetworkAdapters）      |
|                | DNS                                |
|                | 网关（Gateway）                    |
|                | 网络连接状态（NetworkConnection）  |
| 内存（Memory） | 内存类型（Type）                   |
|                | 内存总空间（Total）                |
|                | 已使用（Used）                     |
|                | 空闲空间（Free）                   |
|                | 共享内存空间（Shared）             |
|                | 缓冲/缓存（BuffersOrCached）       |
|                | 可用空间（Available）              |

**系统参数检查**

|  检查项| 检查内容|
| ---------------------- | ------------------------------- |
| 基础信息（BaseInfo）   | 操作系统名称（OS）              |
|                        | 操作系统版本（Release）         |
|                        | 操作系统内核（Kernel）          |
|                        | 服务器名称（Hostname）            |
|                        | 安全增强型服务（SELinux）       |
|                        | IO调度策略（IOScheduleModel）   |
|                        | 语言与编码（LanguageAndEncode） |
|                        | 当前时间（TimeNow）             |
|                        | 最后启动时间（LastBootTime）    |
|                        | 运行时间（UpTime）              |
| 系统参数（Sysctl）     | DirtyBackgroundRatio            |
|                        | DirtyExpireCentisecs            |
|                        | DirtyRatio                      |
|                        | MaxMapCount                     |
|                        | MinFreeKbytes                   |
|                        | OvercommitMemory                |
|                        | OvercommitRatio                 |
|                        | Swappiness                      |
|                        | VfsCachePressure                |
|                        | IPV4LocalPortRange              |
| 资源限制参数（Ulimit） | CoreFileSize                    |
|                        | DatasSegSize                    |
|                        | SchedulingPriority              |
|                        | FileSize                        |
|                        | PendingSignals                  |
|                        | MaxLockedMemory                 |
|                        | MaxMemorySize                   |
|                        | OpenFiles                       |
|                        | PipeSize                        |
|                        | POSIXMessageQueues              |
|                        | RealTimePriotity                |
|                        | StackSize                       |
|                        | CpuTime                         |
|                        | MaxUserProcesses                |
|                        | VirtualMemory                   |
|                        | FileLocks                       |
| 用户检查（UserInfo）   | 特权用户（PrivilegeUsers）      |
|                        | sudo权限用户（Sudoers）         |
|                        | 用户                            |

**服务检查**

|  检查项| 检查内容|
| ------------------------------- | --------------------------------------- |
| 防火墙（Firewall）              | 开机自启（Enabled）                     |
|                                 | 运行状态（Status）                      |
|                                 | 开放端口（OpenPorts）                   |
| 监听检查（Listening）           | 监听检查（Listening）                   |
| 开机自启的服务（EnableService） | 开机自启的服务（EnableService）         |
| SSH连接信息（SSH）              | 开机自启（Enabled）                     |
|                                 | 状态（Status）                          |
|                                 | 协议（Protocol）                        |
|                                 | 允许使用root用户登录（PermitRootLogin） |
| 运行的服务（RunningService）    | 运行的服务（RunningService）            |

**资源消耗**

|  检查项| 检查内容|
| --------------------- | --------------------- |
| CPU消耗（CPUTop）     | CPU消耗（CPUTop）     |
| 内存消耗（MemoryTop） | 内存消耗（MemoryTop） |

**磁盘IO测试**（默认不开启）

以下检查内容仅展示在前端，因前端内容展示有限，若需要更多检查内容可以打开json文件查看。

|  检查项| 检查内容|
| -------- | ------------------------------------- |
| 基础信息 | fio版本（fio version）                |
|          | 测试时间（time）                      |
|          | 测试参数（global options）            |
| 作业     | 作业名称（jobname）                   |
|          | 目录（directory）                     |
|          | 持续时间（elapsed）                   |
|          | 文件系统块大小（bs）                  |
|          | 文件总大小（size）                    |
|          | 读写方式（rw）                        |
|          | 读写混合（rwmixread）                 |
|          | 运行时间（runtime）                   |
|          | IO读取数据量（io_kbytes）             |
|          | 读取带宽（bw）                        |
|          | 读取吞吐量（iops）                    |
|          | IO读取总次数（total_ios）             |
|          | IO写入数据量（io_kbytes）             |
|          | 写入带宽（bw）                        |
|          | 写入吞吐量（iops）                    |
|          | IO写入总次数（total_ios）             |
|          | 平均的io队列深度统计（iodepth_level） |
| CPU状态  | 用户态CPU占用率（usr_cpu）            |
|          | 内核态CPU占用率（sys_cpu）            |
|          | 测试过程上下文切换次数（ctx）         |
| 磁盘状态 | 磁盘名称（name）                      |
|          | 读IO请求次数（read_ios）              |
|          | 写IO请求次数（write_ios）             |
|          | 磁盘队列花费的所有时间（in_queue）    |
|          | 磁盘利用率（util）                    |

**网络IO测试**（默认不开启）

|  检查项| 检查内容|
| -------- | -------------- |
| 基础信息 | 监听端口等信息 |
| 测试结果 | 每秒传输数据量 |

### 检查步骤

目前服务器检查分为三种检查方式：

- 当前服务器执行检查。
- 基于toml文件内的服务器信息，SSH连接服务器后执行检查。
- 使用agent执行服务器检查。

服务器检查当前支持使用检查文件来控制是否需要检查某些检查项，详情可以查看yascheck.toml文件的示例，修改其中某些检查项参数为ignore则可以选择是否检查。

#### 当前服务器检查

当前服务器检查，执行成功后会生成一个summary.json文件和一个服务器检查结果的JSON文件。服务器检查结果文件可以自定义名称，若不指定自定义名称，默认为yascheck-local-时间戳.json。

```bash
$ yasboot collection host
```

可以自定义输出的文件夹和文件名，例如在/home/yashan/check目录生成报告文件，其中服务器检查文件名为yashan1.json。

```bash
$ yasboot collection host -o /home/yashan/check/ -n yashan1
```

指定检查仅生成JSON报告或生成html报告和JSON报告（指定生成html报告一定会同时生成JSON报告）。

```bash
$ yasboot collection host -F json
$ yasboot collection host -F html
```

指定生成结果为html时，会生成三个文件。

|  文件| 描述|
| ----------------------- | ------------------------------------------------------------ |
| summary.json            | 报告汇总文件，里面包含了文件的分数以及集成的检查告警项目，此文件名不可更改。 |
| yascheck-local-xxx.json | 此文件为检查结果的内容，可通过--name参数自定义文件名。         |
| yasreport.html          | 此文件为检查报告和检查结果的内容前端展示，此文件名不可更改。 |

使用yascheck.toml文件进行检查。

```bash
$ yasboot collection host -F html -t yascheck.toml
```

使用yascheck.toml生成前端结果报告，并命名为yashan2输出到/home/yashan/check目录。

```bash
$ yasboot collection host -F html -t yascheck.toml -o /home/yashan/check -n yashan2
```

#### SSH连接服务器并执行检查

通过SSH连接toml文件内记录的服务器并执行检查，会生成与服务器个数对应的服务器检查文件和一个汇总的文件summary.json文件。

```bash
$ yasboot collection host --host hosts.toml
```

指定生成html报告。

```bash
$ yasboot collection host --host hosts.toml -F html
```

使用自定义的yascheck.toml进行检查。

```bash
$ yasboot collection host --host hosts.toml -F html -t yascheck.toml
```

#### 使用yasagent执行服务器检查

使用yasagent执行服务器检查，会生成一个xxx.tar.gz的压缩文件，文件夹内容同上。

```bash
$ yasboot collection host -c yashan
```

若仅需检查其中一台服务器，可以指定该服务器的hostid。

```bash
$ yasboot collection host -c yashandb --host host0001
```

指定生成html报告。

```bash
$ yasboot collection host -c yashandb -F html
```

使用自定义的yascheck.toml进行检查。

```bash
$ yasboot collection host -c yashandb -F html -t yascheck.toml
```

## collection sql

本命令用于在指定yasdb节点上执行收集SQL并生成报告。

|  选项| 含义|
| -------------------- | ------------------------------------------------------------ |
| *-c,--cluster*       | YashanDB的集群名                                             |
| *\-F,--format*       | SQL收集的结果格式化，可选json和html                          |
| *\-o,--output*       | SQL收集结果和报告的输出目录                                  |
| *\-n,--name*         | SQL收集结果和报告的标识名称                                  |
| *\-t,--toml*         | 使用toml文件内的配置项，默认为conf/sqlcollect.toml           |
| *\-a,--addr*         | 指定节点的地址，可以为`IP:port`或YASDB_DATA的路径     |
| *\-p,--sys-password* | sys用户密码                                                  |
| *\--node-ids*        | SQL收集的节点ID，可填写多个并通过英文逗号`,`隔开，默认为`1-1`（可以使用`all`表示收集所有节点） |
| *--disable*          | 屏蔽任务进度输出                                             |
| *-d,--child*         | 展示包含子任务执行信息                                       |

示例

```shell
# 执行本机的SQL收集
$ yasboot collection sql --addr 127.0.0.1:1688 --sys-password password

# 指定YASDB_DATA路径，通过本地免密的方式，执行本机的SQL收集。本地免密需要添加用户组：YASDBA
$ yasboot collection sql --addr ${YASDB_DATA} -F html

# 使用toml文件的配置项进行收集
$ yasboot collection sql -t sqlcollect.toml --addr 127.0.0.1:1688 --sys-password password

# 执行本机的服务器检查并且生成前端报告输出到规定目录
$ yasboot collection sql -F html --output /home/yashan/check --addr ${YASDB_DATA}

# 执行集群节点1-1的SQL收集
$ yasboot collection sql -c yashandb --sys-password password

# 执行集群节点2-1和3-2的SQL收集
$ yasboot collection sql -c yashandb --sys-password password --node-ids 2-1,3-2

# 执行集群所有节点的SQL收集
$ yasboot collection sql -c yashandb --sys-password password --node-ids all
```

## collection gstack

本命令用于收集服务器上yasdb的gstack信息。

> **Note**:
>
> gstack需要由用户自行安装在对应服务器上，且执行程序可在SSH远程执行命令时被正确找到。
>
> 若确认安装但无法识别的，请检查环境变量PATH的配置。

|  选项| 含义|
| -------------- | ------------------------------ |
| *-c,--cluster* | YashanDB的集群名               |
| *\-o,--output* | gstack收集结果和报告的输出目录 |
| *\-n,--name*   | gstack收集结果和报告的标识名称 |
| *--disable*    | 屏蔽任务进度输出               |
| *-d,--child*   | 展示包含子任务执行信息         |

示例

```shell
# 执行本机的gstack收集
$ yasboot collection gstack

# 指定YASDB_DATA路径，通过本地免密的方式，执行本机的SQL收集。本地免密需要添加用户组：YASDBA
$ yasboot collection gstack --output /home/yashan/check -n yashan

# 检查agent服务器上所有yasdb进程
$ yasboot collection gstack -c  yashan
```

## collection all

本命令用于一键收集服务器、SQL、gstack、磁盘IO以及网络信息，仅支持通过yasagent进行收集。

|  选项| 含义|
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*        | YashanDB的集群名                                             |
| *\-F,--format*        | 服务器检查的结果格式化，可选json和html                         |
| *\-o,--output*        | 服务器检查结果和报告的输出目录                                 |
| *\-n,--name*          | 服务器检查结果和报告的标识名称                                 |
| *\-y,--yascheck-toml* | 使用toml文件内的配置项，格式请查阅[服务器检查配置文件](../配置文件/服务器检查配置文件)                    |
| *\-s,--sql-toml*      | 使用toml文件内的配置项                                       |
| *\--node-ids*         | SQL收集的节点ID，多个ID间用逗号`,`隔开，默认为`1-1`（可以使用`all`表示收集所有节点） |
| *\-p,--sys-password*  | sys用户密码                                                  |
| *\--gstack*           | 是否收集gstack信息，默认不收集                                  |
| *--disable*           | 屏蔽任务进度输出                                             |
| *-cl,--cluster-log*       | 收集数据库集群中所有节点上的全部日志信息和数据库配置文件           |
| *-cls,--cluster-log-start*       | 收集数据库日志信息的开始时间，默认为当天零点               |
| *-cle,--cluster-log-end*        | 收集数据库日志信息的结束时间，默认为当前时间                             |
| *-f, --force*                  |	收集数据日志信息，无需确认起始时间和终止时间  |
| *-d,--child*          | 展示包含子任务执行信息                                       |
| *--no-download*        | 不下载到本地（隐藏参数）                                               |
| *--check-module*       | 检查模块，例如SQL、GSTACK或HOST，多个模块间使用逗号`,`隔开（隐藏参数）                                                  |

示例

```shell
yasboot collection all -c yashandb --sys-password password -d -F html
```

收集trace等数据库日志信息。
```shell
yasboot collection all -c yashandb --sys-password password -d -cl 
```
