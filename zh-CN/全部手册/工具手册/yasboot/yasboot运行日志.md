yasboot工具在使用过程中根据不同的进程会产生三种不同的运行日志。

## yasboot日志

主要记录yasboot执行命令过程中在本地产生的关键信息（若流程已经到yasom进程或yasagent进程需查看相应进程的日志），帮助运维人员定位并处理执行命令过程中出现的问题。

**日志级别**

yasboot日志的级别为DEBUG。

**日志文件大小上限**

yasboot日志的文件大小上限为100M，且无法调整。文件容量触达上限时会对文件进行切割生成多个日志文件。

**日志文件数量**

yasboot日志的文件最多保留30个，且无法调整。文件数量触达上限时会自动删除早期的文件。

**日志存放路径**

yasboot日志文件的默认存放路径为{yasboot工具所在路径}/om/yasboot.log：

- yasboot工具初始位于YashanDB安装路径（即安装包解压位置，此时日志存放路径为/home/yashan/install/om/yasboot.log）。

- 待YashanDB安装完成且配置完环境变量后，再使用yasboot工具生成的相关日志就会存放在$YASDB_HOME/om/yasboot.log路径。

yasboot日志存放路径示例如下：

```shell
# 以yasboot工具初始位于YashanDB安装路径为例
$ cd /home/yashan/install
$ ll bin/
total 0
lrwxrwxrwx 1 yashan yashan 17 Apr 17 00:15 yasboot -> ../om/bin/yasboot
# yasboot.log将出现在om目录下
# 执行命令前的om目录如下
$ ll om/
total 0
drwxrwxr-x 2 yashan yashan  135 Apr 17 00:14 bin
drwxrwxr-x 2 yashan yashan  156 Apr 17 00:14 conf
drwxrwxr-x 2 yashan yashan   30 Apr 17 00:14 monit
drwxrwxr-x 3 yashan yashan   38 Apr 17 00:14 static
# 执行yasboot命令
$ ./bin/yasboot package se gen -c yashandb -L
# 执行命令后的om目录如下，出现了yasboot.log
$ ll om/
total 16
drwxrwxr-x 2 yashan yashan  135 Apr 17 00:14 bin
drwxrwxr-x 2 yashan yashan  156 Apr 17 00:14 conf
drwxrwxr-x 2 yashan yashan   30 Apr 17 00:14 monit
drwxrwxr-x 3 yashan yashan   38 Apr 17 00:14 static
-rw-rw-r-- 1 yashan yashan 4294 Apr 22 18:06 yasboot.log
```

## yasom日志

主要记录yasom进程在处理任务过程中的关键信息，帮助运维人员定位并处理执行命令过程中出现的问题。

**日志级别**

yasom日志的级别默认为DEBUG。

**日志文件大小上限**

yasom日志的文件大小上限为100M。文件容量触达上限时会对文件进行切割生成多个日志文件。

**日志文件数量**

yasom日志的文件最多保留30个。文件数量触达上限时会自动删除早期的文件。

**日志存放路径**

yasom日志文件的默认存放路径为{$YASDB_HOME上一级目录}/log/om/{集群名称}/yasom.log：

日志存放路径可按需进行[规划](../../安装和升级/安装部署/安装前准备/创建用户和目录.html#run_log_path)，存放路径指定/调整方法包括：

- 方式1：安装YashanDB过程中（执行yasboot package install前），生成部署文件时通过--log-path参数指定日志存放路径。该操作会同时指定数据库的run.log、slow.log、yasom日志和yasagent日志的存放目录，如需分开存放请结合实际进行调整。

- 方式2：安装YashanDB过程中（执行yasboot package install前），生成部署文件后，修改hosts.toml文件中[host]下的log_path参数指定日志存放路径。该操作会同时指定yasom日志和yasagent日志的存放目录，如需分开存放请结合实际进行调整。

    ```toml
    # 文件内容仅为节选
    [[host]]
    hostid = "host0001"
    user = "yashan"
    password = "password"
    ip = "192.168.1.2"
    port = 22
    path = "/data/yashan/yasdb_home"
    log_path = "/data/yashan/log"       # yasom和yasagent的日志
    [host.yasagent]
        [host.yasagent.config]
        LISTEN_ADDR = "192.168.1.2:1676"
    ```

- 方式3：YashanDB安装完成后（执行yasboot package install后），修改$YASDB_HOME/om/{集群名称}/conf/yasom.toml文件中[log]下的log_path参数指定日志存放路径，修改后需重启yasom使配置生效。

    ```toml
    # 文件内容仅为节选
    [log]
      level = 0
      max_age = 7s
      max_size = 104857600
      max_backups = 30
      path = "/data/yashan/log/om/yashandb"
      log_path = "/data/yashan/log/om/yashandb/yasom.log"
      err_log_path = "/data/yashan/log/om/yashandb/yasom-panic.log"
      start_log = "/data/yashan/log/om/yashandb/yasom-start.log"
      console = true
    ```

## yasagent日志

主要记录yasagent进程在处理任务过程中的关键信息，帮助运维人员定位并处理执行命令过程中出现的问题。

**日志级别**

yasagent日志的级别默认为DEBUG。

**日志文件大小上限**

yasagent日志的文件大小上限为100M。文件容量触达上限时会对文件进行切割生成多个日志文件。

**日志文件数量**

yasagent日志的文件最多保留30个。文件数量触达上限时会自动删除早期的文件。

**日志存放路径**

yasagent日志文件的默认存放路径为{$YASDB_HOME上一级目录}/log/om/{集群名称}/yasagent.log：

日志存放路径可按需进行[规划](../../安装和升级/安装部署/安装前准备/创建用户和目录.html#run_log_path)，存放路径指定/调整方法包括：

- 方式1：安装YashanDB过程中（执行yasboot package install前），生成部署文件时通过--log-path参数指定日志存放路径。该操作会同时指定数据库的run.log、slow.log、yasom日志和yasagent日志的存放目录，如需分开存放请结合实际进行调整。

- 方式2：安装YashanDB过程中（执行yasboot package install前），生成部署文件后，修改hosts.toml文件中[host]下的log_path参数指定日志存放路径。该操作会同时指定yasom日志和yasagent日志的存放目录，如需分开存放请结合实际进行调整。

    ```toml
    # 文件内容仅为节选
    [[host]]
    hostid = "host0001"
    user = "yashan"
    password = "password"
    ip = "192.168.1.2"
    port = 22
    path = "/data/yashan/yasdb_home"
    log_path = "/data/yashan/log"       # yasom和yasagent的日志
    [host.yasagent]
        [host.yasagent.config]
        LISTEN_ADDR = "192.168.1.2:1676"
    ```

- 方式3：YashanDB安装完成后（执行yasboot package install后），修改$YASDB_HOME/om/{集群名称}/conf/yasagent.toml文件中[log]下的log_path参数指定日志存放路径，修改后需重启yasagent使配置生效。

    ```toml
    # 文件内容仅为节选
    [log]
      level = 0
      max_age = 7
      max_size = 104857600
      max_backups = 30
      path = "/data/yashan/log/om/yashandb"
      log_path = "/data/yashan/log/om/yashandb/yasagent.log"
      err_log_path = "/data/yashan/log/om/yashandb/yasagent-panic.  log"
      start_log = "/data/yashan/log/om/yashandb/yasagent-start.log"
      console = true
    ```
