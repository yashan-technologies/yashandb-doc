命令格式
----

```shell
yasboot {SUBCOMMAND1} {SUBCOMMAND2} {OPTIONS}
```

一级命令表示yasboot提供的模块支持，二级命令表示操作。

SUBCOMMAND1：

| 选项         | 含义                                       |
| ------------ | ------------------------------------------ |
| *cluster*    | 数据库集群管理命令                         |
| *collection* | 一键收集命令                               |
| *config*     | 数据库集群配置管理命令                     |
| *dataspace*  | 数据库重分布管理命令                       |
| *discovery*  | 数据库AC发现命令                           |
| *election*   | 基于yasom的自动选主配置和管理              |
| *group*      | 对节点组进行管理                           |
| *host*       | 查看服务器的运行状态                         |
| *load*       | 一键拆分和导入CSV数据                      |
| *monit*      | monit管理命令                              |
| *node*       | 对单个节点进行管理                         |
| *package*    | 数据库集群配置和部署命令                   |
| *patrol*     | 数据库巡检管理                             |
| *process*    | 对yasom、yasagent、yasdb进程进行管理 |
| *sql*        | 指定在某个节点上执行yasql                  |
| *task*       | 管理yasboot执行的任务                           |
| *whitelist*  | 白名单管理命令                             |
| *ycs*        | 共享集群YCS管理命令                        |
| *yfs*        | 共享集群YFS管理命令                      |
| *ipchange*   | 更换数据库集群中的服务器IP命令                |
| *job*        | 计划管理命令                              |
| *init*       | 单机数据库快速部署命令                         |
