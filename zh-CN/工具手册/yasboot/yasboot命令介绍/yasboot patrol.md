> **Caution**: 
>
> 需开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（遵循标准安装步骤时默认已开启）才能正常使用巡检功能。

## patrol strategy gen

本命令用于根据输入的各选项生成巡检策略配置文件。

|  选项| 含义|
| ----------------------- | ------------------------ |
| *-c, --cluster*         | YashanDB的集群名（必传参数）           |
| *-s, --strategy-name*   | 巡检策略名称 （必传参数）              |
| *-t, --strategy-type*   | 巡检策略类型：<br/>\* PERIOD：周期性策略，应用后可以周期性执行多次，默认值<br/>\* TIMING：定时巡检策略，应用后定时执行一次 |
| *-ce,--cron-expression* | cron表达式，用于设置巡检时间，由五个字段组成，分别表示分、小时、日期、月份、星期 |
| *-f,--frequency*        | 巡检频率：<br/>\* monthly：每月<br/>\* weekly：每周，默认值<br/>\* daily：每天<br/>\* hourly：每小时 |
| *-d,--days*             | 巡检具体的执行天数    |
| *-st, --start-time*     | 巡检的具体开始时间    |
| *-sp,--store-path*      | 巡检文件存放路径      |
| *-sd,--store-days*       | 巡检文件的最大保存天数，默认全部保存   |
| *-sn,--store-num*       | 巡检文件的最大保存数量，默认全部保存   |
| *--yascheck-toml*      | yascheck.toml配置文件 |
| *--sql-toml*          | sqlcollect.toml配置文件                |
| *--patrol-module*      | 巡检模块，可选`host`、`sql`和`gstack`，默认收集所有模块，填写多个使用符号`,`分割 |
| *-F,--format*          | 检查结果格式，可选json和html           |
| *--node-ids*           | SQL收集的节点id，可填写多个，可使用符号`,`隔开，默认为`1-1`（可以使用`all`表示收集所有节点） |
| *-o,--output*           | 生成的配置文件保存路径，默认是当前路径 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 通过参数`--cron-expression`设置巡检时间并生成配置文件
$ yasboot patrol strategy gen -c yashandb -s ps01  --cron-expression "10 12 ? * 1,5,6,0" 

# 通过参数`--frquency、--days、--start-time`设置巡检时间并生成配置文件
$ yasboot patrol strategy gen -c yashandb -s ps01  -f weekly -d 1,5-7  -st 12:10

# 生成巡检模块为host和gstack的配置文件
$ yasboot patrol strategy gen -c yashandb -s ps01  --cron-expression "10 12 ? * 1,5,6,0" --patrol-module host,gstack
```

## patrol strategy add

本命令用于将巡检策略添加并保存到yasom中。

|  选项| 含义|
| ------------ | ---------------------------- |
| *-t, --toml* | 巡检策略配置文件（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol strategy add -t ps01_patrolStrategy.toml 
```

## patrol strategy apply

本命令用于将巡检策略应用到数据库节点。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-s,--strategy-name* | 巡检策略名称（必传参数）     |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol strategy apply -c yashandb -s ps01 
```

## patrol strategy list

本命令用于展示巡检策略信息。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-s,--strategy-name* | 巡检策略名称                 |
| *--size*             | 查询的每页数据量，默认10     |
| *-p,--page*          | 需要查询的页码               |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 展示指定巡检策略信息
$ yasboot patrol strategy list -c yashandb -s ps01

# 分页展示所有巡检策略信息
$ yasboot patrol strategy list -c yashan
```

## patrol strategy cancel

本命令用于取消应用巡检策略到数据库。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-s,--strategy-name* | 巡检策略名称（必传参数）     |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol strategy cancel -c yashandb -s ps01
```

## patrol strategy delete

本命令用于删除巡检策略。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-s,--strategy-name* | 巡检策略名称（必传参数）     |
| *-f,--force*         | 强制删除                     |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol strategy delete -c yashandb -s ps01
```

## patrol report list

本命令用于分页展示巡检记录。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-s,--strategy-name* | 巡检策略名称                 |
| *--size*             | 查询的每页数据量，默认10     |
| *-p,--page*          | 需要查询的页码               |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol report list -c yashan
```

## patrol report get

本命令用于获取某个巡检报告 。

|  选项| 含义|
| --------------- | ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-u,--uuid*     | 巡检报告的uuid（必传参数）   |
| *-o,--output*   | 巡检报告输出路径             |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot patrol report get -c yashandb -u 28d9d7d2c8ef7717
```

## patrol report delete

本命令用于删除巡检集。

|  选项| 含义|
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *-u,--uuid*          | 巡检报告uuid                 |
| *-s,--strategy-name* | 策略名称                     |
| *-w, --nowait*              | 运行后不等待执行命令结果               |
| *-d, --child*               | 展示任务以及子任务信息|
| *--disable*                 | 屏蔽任务进度条展示    |
| *-h,--help*        | 查看当前命令的帮助信息 |

`-u`和`-s`参数二者必选其一。



示例

```shell
# 删除指定uuid巡检报告
$ yasboot patrol report delete -c yashandb -u 28d9d7d2c8ef7717

# 删除指定巡检策略产生的所有巡检报告
$ yasboot patrol report delete -c yashandb -s ps01
```
