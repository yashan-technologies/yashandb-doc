## package se gen

The command is used to generate a configuration file for Standalone Deployment based on the input options.

|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **Server Configuration Parameters** |  |
| *-u,--username*         | SSH username for the server                  |
| *--groupname*           | User group name, defaults to `username`                  |
| *-p,--password*         | SSH login password          |
| *-N,--no-password*      | SSH passwordless login             |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username (used to execute commands that require sudo privileges, such as creating cgroup directories)    |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password      |
| *--host*                | Server connection information expression           |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`        |
| *--port*                | SSH connection port of the server   |
| **Database Basic Configuration Parameters** |  |
| -c,--cluster       | Name of the generated cluster (required parameter)           |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)         |
| *--data-path*           | DATA directory of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)     |
| *--log-path*            | yasagent, yasom, database run.log, slow.log path                  |
| *--begin-port*          | Starting port, defaults to 1688|
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)              |
| *--replica-cidr*        |  Primary-standby Replication link address (defaults to the same IP segment)              |
| *--table-type*          | Main business table type, optional [HEAP,TAC,LSC], defaults to HEAP  |
| *-m, --mode*            | Syntax mode of the database, options [yashan,mysql]<br/>* yashan: indicates deployment in yashan mode, defaults to this value when omitted, cannot directly switch to mysql mode after installation in yashan mode<br/>* mysql: indicates deployment in mysql mode     |
| **Node Scale Parameters** |  |
| *--node*                | Scale of standalone db group and node deployment, defaults to 1             |
| *--cascade-node*        | Number of Cascade standby nodes |
| *--cascade-parent*      | Index of the Cascade binding standby node                   |
| *--group*               | Number of standalone groups, defaults to 1                  |
| *--standby-node*        | Number of nodes in the standalone standby group                    |
| **Database Performance Parameters** |  |
| *--create-cgroup*       | Whether to create resource management cgroup directory (defaults to no), when enabled at least one of -u, -p or -su, -sp must be specified      |
| *--cgroup-path*         | Resource management cgroup directory (defaults to `/sys/fs/cgroup`)|
| *--recommend-param*     | Whether to enable recommended parameter functionality                        |
| *--memory-limit*        | Maximum percentage limit of memory usable by the server, defaults to 80. Must be used in conjunction with --recommend - param             |
| *--cpu-limit*           | Maximum percentage limit of CPU usable by the server, defaults to 100. Must be used in conjunction with --recommend - param           |
| *--scale-factor*        | Data scale for test scenarios such as TPCH, defaults to 100, indicating 100G test data. Must be used in conjunction with --recommend - param           |
| *--scene*               | Scenario for parameter tuning, optional [NORMAL,BATCH,TPCH], defaults to NORMAL. For OLTP business primarily using HEAP tables, if performance is poor during periodic batch scenarios, consider using BATCH scene parameters for tuning. Must be used in conjunction with --recommend - param |
| **Other Parameters** |  |
| * -h,--help*          | View help information for the current command    |
| *--ssl-protocol*        | SSL protocol, only supports SSL                     |
| *--ssl-path*            | Directory where the SSL key is located                       |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy|
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1      |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4            |
| *-o, --output*          | Output path for configuration file   |
| *--dev*                 | For internal development use only  |
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/LSFA_LISTAGG) function<br>* s3: Install plugins for S3 bucket related functionality<br>* udf: Install plugins for [ UDF ](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/XML Function/00XML Function) function |


<span id="ce" name="ce" class="yaslink"></span>

## package ce gen

The command is used to generate a configuration file for YAC Deployment or Distributed Cluster Deployment based on the input options.


|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **指定服务器信息** |  |
| *-u,--username*       | 服务器SSH用户名                  |
| *--groupname*         | 用户组名称，默认和`username`相同                  |
| *-p,--password*       | SSH登录密码          |
| *-N,--no-password*    | SSH免密登录             |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值（用于执行需要sudo权限的命令，例如创建cgroup目录）    |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值      |
| *--host*              | 服务器连接信息表达式           |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开        |
| *--port*              | 服务器SSH连接端口             |
| **数据库基础信息配置** |  |
| -c,--cluster     | 生成的集群名称（必传参数）           |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的DATA目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）              |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP|
| *-m, --mode*          | 数据库的语法模式，可选项[yashan,mysql]<br/>* yashan：表示部署为yashan模式，省略时默认为该值，以yashan模式安装后无法直接切换至mysql模式<br/>* mysql：表示部署为mysql模式     |
| **节点规模** |  |
| *--node*              | 单机DB组及节点部署的节点规模，默认为1             |
| *--cascade-node*      | 级联备节点个数 |
| *--cascade-parent*    | 级联备绑定备节点的index                   |
| *--group*             | 单机group的个数，默认为1                  |
| *--standby-node*      | 单机备group的节点个数                    |
| **数据库性能相关** |  |
| *--create-cgroup*     | 是否创建资源管理cgroup目录（默认为否），启用时需至少指定-u，-p或者-su，-sp其中之一      |
| *--cgroup-path*       | 资源管理cgroup目录（默认为`/sys/fs/cgroup`）|
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80，须配合--recommend-param使用             |
| *--cpu-limit*         | 服务器的可使用CPU百分比上限，默认为100，须配合--recommend-param使用           |
| *--scale-factor*      | 诸如TPCH等测试场景的数据规模，默认为100，表示100G测试数据须配合--recommend-param使用                     |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优。须配合--recommend-param使用 |
| *--cn-ip*                  | Distributed Cluster Deployment CN IP address, separate multiple IPs with a comma `,`            |
| *--dn-ip*                  | Distributed Cluster Deployment DN IP address, separate multiple IPs with a comma `,`            |
| **数据库基础信息配置** |  |
| -c,--cluster     | 生成的集群名称（必传参数）           |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的DATA目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）              |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP|
| *-m, --mode*          | 数据库的语法模式，可选项[yashan,mysql]<br/>* yashan：表示部署为yashan模式，省略时默认为该值，以yashan模式安装后无法直接切换至mysql模式<br/>* mysql：表示部署为mysql模式     |
| **节点规模** |  |
| *--node*              | 单机DB组及节点部署的节点规模，默认为1             |
| *--cascade-node*      | 级联备节点个数 |
| *--cascade-parent*    | 级联备绑定备节点的index                   |
| *--group*             | 单机group的个数，默认为1                  |
| *--standby-node*      | 单机备group的节点个数                    |
| **数据库性能相关** |  |
| *--create-cgroup*     | 是否创建资源管理cgroup目录（默认为否），启用时需至少指定-u，-p或者-su，-sp其中之一      |
| *--cgroup-path*       | 资源管理cgroup目录（默认为`/sys/fs/cgroup`）|
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80，须配合--recommend-param使用             |
| *--cpu-limit*         | 服务器的可使用CPU百分比上限，默认为100，须配合--recommend-param使用           |
| *--scale-factor*      | 诸如TPCH等测试场景的数据规模，默认为100，表示100G测试数据须配合--recommend-param使用                     |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优。须配合--recommend-param使用 |
| *--data*                | Data diskin storage network, separate multiple data disk information with a comma `,`         |
| *-fg, --failgroup*      | Number of failure groups in YAC disk group (defaults to 1)              |
| *--disk-found-path*     | Disk discovery path (defaults to /dev/yfs)          |
| *--system-data*         | System disk in storage network, separate multiple system disk information with a comma `,`       |
| *--inter-cidr*          | Network communication link address between instances within a YAC (defaults to the same IP segment)|
| *--vips*                | VIP configuration information list of YAC (format: IP address/subnet mask/network card name, e.g., 192.168.60.4/255.255.255.0/ens192), the number of VIPs matches the number of nodes, separate multiple VIP configuration information with a comma `,`      |
| *--trtype*               | NVMe communication protocol mode, rdma or tcp supported |
| **Node Scale Parameters** |  |
| *--node*                | * In YAC Deployments, specifies the number of instances in a single cluster, defaulting to 2<br/>* In Distributed Cluster Deployment, specifies the number of CN nodes in a single cluster, defaulting to 2. CN nodes will be created on the server specified by `--cn-ip` |
| *--group*               | Number of YAC groups, default quantity is 1                  |
| *--standby-node*        | Number of instances in standby cluster during Primary/Standby group deployment, defaults to 2       |
| **其他参数** |  |
| * -h,--help*        | 查看当前命令的帮助信息  |
| *--ssl-protocol*      | SSL协议，仅支持SSL                     |
| *--ssl-path*          | SSL秘钥所在的目录                       |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署|
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，IP默认为127.0.0.1      |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4            |
| *-o, --output*        | 配置文件的输出路径  |
| *--dev*               | 仅内部开发使用 |
| *--plugins*           |  |
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/LSFA_LISTAGG) function(corresponding functionality is not applicable to YAC Deployment, no need to specify installation of this plugin separately)<br>* s3: Install plugins for S3 bucket related functionality(corresponding functionality is not applicable to YAC Deployment, no need to specify installation of this plugin separately)<br>* udf: Install plugins for [ UDF ](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/XML Function/00XML Function) function |

<span id="de" name="de" class="yaslink"></span>

## package de gen

The command is used to generate a configuration file for ISC Distributed Cluster Deployment based on the input options.


|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **指定服务器信息** |  |
| *-u,--username*       | 服务器SSH用户名                  |
| *--groupname*         | 用户组名称，默认和`username`相同                  |
| *-p,--password*       | SSH登录密码          |
| *-N,--no-password*    | SSH免密登录             |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值（用于执行需要sudo权限的命令，例如创建cgroup目录）    |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值      |
| *--host*              | 服务器连接信息表达式           |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开        |
| *--port*              | 服务器SSH连接端口             |
| **数据库基础信息配置** |  |
| -c,--cluster     | 生成的集群名称（必传参数）           |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的DATA目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）              |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP|
| *-m, --mode*          | 数据库的语法模式，可选项[yashan,mysql]<br/>* yashan：表示部署为yashan模式，省略时默认为该值，以yashan模式安装后无法直接切换至mysql模式<br/>* mysql：表示部署为mysql模式     |
| **节点规模** |  |
| *--node*              | 单机DB组及节点部署的节点规模，默认为1             |
| *--cascade-node*      | 级联备节点个数 |
| *--cascade-parent*    | 级联备绑定备节点的index                   |
| *--group*             | 单机group的个数，默认为1                  |
| *--standby-node*      | 单机备group的节点个数                    |
| **数据库性能相关** |  |
| *--create-cgroup*     | 是否创建资源管理cgroup目录（默认为否），启用时需至少指定-u，-p或者-su，-sp其中之一      |
| *--cgroup-path*       | 资源管理cgroup目录（默认为`/sys/fs/cgroup`）|
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80，须配合--recommend-param使用             |
| *--cpu-limit*         | 服务器的可使用CPU百分比上限，默认为100，须配合--recommend-param使用           |
| *--scale-factor*      | 诸如TPCH等测试场景的数据规模，默认为100，表示100G测试数据须配合--recommend-param使用                     |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优。须配合--recommend-param使用 |
| **数据库基础信息配置** |  |
| -c,--cluster     | 生成的集群名称（必传参数）           |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的DATA目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）              |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP|
| *-m, --mode*          | 数据库的语法模式，可选项[yashan,mysql]<br/>* yashan：表示部署为yashan模式，省略时默认为该值，以yashan模式安装后无法直接切换至mysql模式<br/>* mysql：表示部署为mysql模式     |
| **节点规模** |  |
| *--node*              | 单机DB组及节点部署的节点规模，默认为1             |
| *--cascade-node*      | 级联备节点个数 |
| *--cascade-parent*    | 级联备绑定备节点的index                   |
| *--group*             | 单机group的个数，默认为1                  |
| *--standby-node*      | 单机备group的节点个数                    |
| **数据库性能相关** |  |
| *--create-cgroup*     | 是否创建资源管理cgroup目录（默认为否），启用时需至少指定-u，-p或者-su，-sp其中之一      |
| *--cgroup-path*       | 资源管理cgroup目录（默认为`/sys/fs/cgroup`）|
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80，须配合--recommend-param使用             |
| *--cpu-limit*         | 服务器的可使用CPU百分比上限，默认为100，须配合--recommend-param使用           |
| *--scale-factor*      | 诸如TPCH等测试场景的数据规模，默认为100，表示100G测试数据须配合--recommend-param使用                     |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优。须配合--recommend-param使用 |
| *--din-cidr*            | Distributed network communication link address (defaults to the same IP segment)        |
| *--table-type*          | Main business table type, optional [HEAP,TAC,LSC], defaults to LSC                       |
| **Node Scale Parameters** |  |
| *-d, --deploy-mode*     | Deployment scale mode for ISC Distributed Cluster Deployment:<br>\* MINI: Minimum scale deployment, deployed 1MN 1CN 3DN on the same server<br>\* NORMAL: Regular deployment, based on the following node scale               |
| *--mn*                  | Scale of distributed MN type nodes, defaults to 1           |
| *--cn*                  | Scale of distributed CN type nodes, defaults to 1           |
| *--dn*                  | Scale of distributed DN group and nodes, for example, 1-3 means 1 group with 3 nodes (1 master and 2 standby within the group)                  |
| **数据库性能相关** |  |
| *--create-cgroup*     | 是否创建资源管理cgroup目录（默认为否），启用时需至少指定-u，-p或者-su，-sp其中之一      |
| *--cgroup-path*       | 资源管理cgroup目录（默认为`/sys/fs/cgroup`）|
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80，须配合--recommend-param使用             |
| *--cpu-limit*         | 服务器的可使用CPU百分比上限，默认为100，须配合--recommend-param使用           |
| *--scale-factor*      | 诸如TPCH等测试场景的数据规模，默认为100，表示100G测试数据须配合--recommend-param使用                     |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优。须配合--recommend-param使用 |
| *--scene*               | Scenario for parameter tuning, optional [NORMAL,TPCH], defaults to NORMAL. When performing TPC-H performance testing with distributed LSC table, consider using TPCH scene parameter for tuning, and ensure that all nodes perform parameter recommendation, otherwise it may fail due to connection parameter mismatch during reboot. Must be used in conjunction with --recommend - param   |
| **其他参数** |  |
| * -h,--help*        | 查看当前命令的帮助信息  |
| *--ssl-protocol*      | SSL协议，仅支持SSL                     |
| *--ssl-path*          | SSL秘钥所在的目录                       |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署|
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，IP默认为127.0.0.1      |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4            |
| *-o, --output*        | 配置文件的输出路径  |
| *--dev*               | 仅内部开发使用 |
| *--plugins*           |  |
| *--node-info*           | Specify the file path of node deployment information, this parameter is used to restore backup sets to an empty cluster, obtained through the *yasrman*'s [View Backup Sets](../../yasrman/User Guide for yasrman/00User Guide for yasrman).     |
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/LSFA_LISTAGG) function(corresponding functionality is not applicable to ISC Distributed Cluster Deployment, no need to specify installation of this plugin separately)<br>* s3: Install plugins for S3 bucket related functionality<br>* udf: Install plugins for [ UDF ](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/XML Function/00XML Function) function |

## package config join-demo

This command is used to generate a configuration file template `join_demo.toml` for managed command usage.

|Option |Meaning |
| ------------ | ----------------------------------- |
| *-t, --type*   | Type of the database cluster to be managed (required parameter), SE represents Standalone Deployment, DE represents ISC Distributed Cluster Deployment  |
| *-d, --dir*    | Path where the configuration file will be generated (defaults to current directory)  |

***Example***

```shell
$ yasboot package config join-demo -t SE
```

## package config show

This command is used to display the generated cluster configuration file information.

|Option |Meaning |
|-----------------| ------------------------------- |
| *-c, --cluster*     | Name of the YashanDB deployed cluster (hidden parameter)         |
| *-t, --toml*        | Name of the cluster configuration file to be displayed (e.g., yashandb.toml) (hidden parameter) |
| *-b, --by*          | Display by server/node group, defaults to host (hidden parameter) |

***Example***

```shell
$ yasboot package config show -c yashandb
```

## package upload

This command is used to update the information in `hosts.toml` and upload a new YashanDB software package.

|Option |Meaning |
| ------------------- | -------------------------- |
| *-t, --toml*          | toml file to be updated (required parameter) |
| *-i, --install-pkg*   | Path of the new installation package (deprecated parameter)             |

***Example***

```shell
$ yasboot package upload -t hosts.toml
```

## package install

This command is used to install the software on a remote server (it can install on all or specific servers according to the toml file configuration), the package is in tar.gz format.

|Option |Meaning |
| -------------------- | ------------------------------------ |
| *\-i, --install-pkg*   | Local path of the software package (deprecated parameter)       |
| *--plugin*             | Local path of the plugin package (deprecated parameter)     |
| *--deps*               | Local path of the dependency package (hidden parameter)               |
| *-f, --force*          | Ignore errors and force installation, defaults to false      |
| *\-t, --toml*          | Configuration file of the server related to the software package to be installed |
| *--disable*            | Suppress task progress bar display                   |

***Example***

```shell
$ yasboot package install -t hosts.toml
```

## package uninstall

This command is used to uninstall the installed software on a remote server (it can uninstall on all or specific servers according to the toml file configuration).

|Option |Meaning |
| ---------------- | ------------------------------------------- |
| *\-c, --cluster*   | Name of the YashanDB deployed cluster (required parameter)            |
| *-t, --toml*       | Uninstall via SSH, requires server configuration file such as hosts.toml |
| *-d, --child*      | Display task and sub-task details                      |
| *-f, --force*      | Force uninstall, default false|
| *--disable*        | Suppress task progress bar display |

***Example***

```shell
# Uninstall the entire cluster
$ yasboot package uninstall -c yashandb

# Uninstall based on configuration file
$ yasboot package uninstall -c yashandb -t hosts.toml
```

## package upgrade

This command is used to upgrade the installed software (yasom, yasagent) on a remote server.
It must be executed after the database upgrade is completed.

|Option |Meaning |
| ------------- | ---------------------------------------- |
| *-t, --toml*    | Server configuration file such as hosts.toml (required parameter) |
| *--package*     | Absolute path of the upgrade package (deprecated parameter)         |
| *--plugin*      | Local path of the plugin package (deprecated parameter)        |
| *-d, --child*   | Display task and sub-task details                   |
| *--disable*     | Suppress task progress bar display                       |
| *--deps*        | Local path of the dependency package (hidden parameter)        |
| *--same-version*| Same version upgrade (hidden parameter)        |
| *--only-replace-without-version-limit*    | Ignore version checks and directly replace binaries, this parameter is a high-risk operation, **do not use in production environments** (hidden parameter)  |

***Example***

```shell
$ yasboot package upgrade -t hosts.toml
```

## package rollback

This command is used to roll back the installed software (yasom, yasagent) on a remote server.

|Option |Meaning |
| ---------------- | ---------------------------------------- |
| *\-c, --cluster*   | Name of the YashanDB deployed cluster (required parameter)         |
| *-t, --toml*       | Server configuration file such as hosts.toml (required parameter) |
| *--disable*        | Suppress task progress bar display                       |

***Example***

```shell
$ yasboot package rollback -c yashandb -t hosts.toml
```

## package verify

This command is used to verify whether the installation package has been tampered with.

***Example***

```shell
# After decompressing the installation package, use the *yasboot* in the installation package to execute the following command
$ yasboot package verify
```

## package host

This command is used to modify the configuration information in the server configuration file, currently only the password can be modified.

|Option |Meaning |
|-----------------|-------------------------------------|
| *-t, --toml*        | Server configuration file to be modified (e.g., hosts.toml) (required parameter) |
| *-k, --key*         | Name of the configuration to be modified (required parameter)                       |
| *-v, --value*       | Value of the configuration to be modified (required parameter)                |
| *--host-id*         | ID of the server where the information needs to be modified (hidden parameter, leave blank to modify all)                    |

***Example***

```shell
$ yasboot package host -t hosts.toml -k password -v 123 --host-id host0001
```


## package config gen

This command is used to generate a configuration file based on the input options.

This command will no longer be maintained in the next major version, please use the package se/de/ce gen command instead.

|Option |Meaning |
| --------------------- | ------------------------------------------- |
| *\-c,--cluster*         | Name of the generated cluster (required parameter)            |
| *-u,--username*         | SSH username for the server|
| *--groupname*           | User group name, defaults to `username`      |
| *-p,--password*         | SSH login password  |
| *-N,--no-password*      | SSH passwordless login  |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`                      |
| *--port*                | SSH connection port of the server                       |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)                       |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username (used to execute commands that require sudo privileges, such as creating cgroup directories)                   |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password             |
| *--no-add-yasdba*       | Do not add the installer user to the YASDBA user group          |
| *--boot-start-monit*    | Enable self-starting monit daemon (add startup command to /etc/rc.local)         |
| *--host*                | Server connection information expression                    |
| *-t, --yas-type*        | Deployment shape of the database:<br>\* SE: Standalone Deployment <br>\* CE: YAC Deployment <br>\* DE: ISC Distributed Cluster Deployment, default value |
| *-d, --deploy-mode*     | Deployment scale of ISC Distributed Cluster Deployment:<br>\* MINI: Minimum scale deployment, deployed 1MN 1CN 3DN on the same server<br>\* NORMAL: Regular deployment, based on the following node scale |
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1                     |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4    |
| *--mn*                  | Scale of MN nodes within ISC Distributed Cluster Deployment, defaults to 1   |
| *--cn*                  | Scale of CN nodes within ISC Distributed Cluster Deployment, defaults to 1   |
| *--dn*                  | Scale of DN group and nodes within ISC Distributed Cluster Deployment, for example, 1-3 means a group with 3 nodes (1 master and 2 standby within the group)|
| *--db*                  | Scale of nodes in standalone DB group, defaults to 1 |
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)  |
| *--din-cidr*            | Distributed network communication link address (defaults to the same IP segment)                     |
| *--replica-cidr*        | Primary-standby Replication link address (defaults to the same IP segment)  |
| *--begin-port*          | Starting port 1688 |
| *--data-path*           | DATA directory of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)                 |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy               |
| *-o, --output*          | File output path               |
| *--ce*                  | Scale of nodes in YAC type, defaults to 2, maximum 8         |
| *--inter-cidr*          | Internal communication link address of YAC (defaults to the same IP segment)               |
| *--ce-data*             | Data disk in YAC, separate multiple data disk information with a comma `,`                       |
| *--create-cgroup*       | Whether to create resource management cgroup directory (defaults to no), when enabled, at least one of -u, -p or -su, -sp must be specified                  |
| *--cgroup-path*         | Resource management cgroup directory (defaults to `/sys/fs/cgroup`)                   |
| *--recommend-param*     | Whether to enable recommended parameter functionality                   |
| *--memory-limit*        | Maximum percentage limit of memory usable by the server, defaults to 80            |
| *--table-type*          | Make database configuration parameters more suitable for the main business table type, optional [HEAP,TAC,LSC], defaults to HEAP in Standalone Deployment and LSC in ISC Distributed Cluster Deployment          |
| *-fg, --failgroup*      | Number of failure groups in YAC disk group (defaults to 1)          |
| *--ce-group*            | Number of YAC groups, unique parameter in YAC Deployment, default quantity is 1          |
| *--disk-found-path*     | YAC disk discovery path (defaults to /dev/yfs)                      |

Parameter explanations:

- IP: The abbreviation `192.168.157.[1-3]` represents `192.168.157.1, 192.168.157.2, 192.168.157.3` three consecutive IP addresses. If it is an IPv6 address, it needs to be in square brackets, and if it is a link-local address, it also needs to include the network card number. IP can be configured as the following four combinations:
    - IPv4 address
    - IPv6 address
    - IPv4 address with IPv4-mapped IPv6 address (`[::ffff:192.168.157.1]`)
	- IPv6 address with IPv4-mapped IPv6 address
- CIDR: Multiple segments can be configured via `--listen-cidr 0.0.0.0/0,192.168.1.0/22`.
- Password: If SSH passwordless login is configured, the password is optional.

```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip 192.168.157.1,192.168.157.2,192.168.157.3 --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip [fc00:157::129],[fc00:157::130],[fc00:157::131]   --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip [::1],[fe80::20c:29ff:fe75:582b%ens33],[fc00:157::129]  --port 22 --install-path /var/database/yashan --begin-port 1688
```

The above commands are equivalent to:

```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip 192.168.157.[1-3] --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip  [fc00:157::[129-131]] --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb --host yashan:password@[::1],[fe80::20c:29ff:fe75:582b%ens33],[fc00:157::129]:22:/var/database/yashan --begin-port 1688
```

Using server connection information expression:

- Full parameters: `username:password@IP1,IP2,IP3:port:install-path`
- In the case of SSH passwordless configuration, the password can be empty: `username:@IP1,IP2,IP3:port:install-path`
- IP supports abbreviation for consecutive IP addresses: `username:@192.168.157.[1-3]:port:install-path`
- The port can be omitted: `username:password@IP1,IP2,IP3:install-path`
- The installation path can be omitted: `username:password@IP1,IP2,IP3:port`

```shell
$ yasboot package config gen -c yashandb \
--host yashan:password@IP1,IP2,...,IPn:22:/var/database/yashan --begin-port 1688
```
- Disk discovery path: When using a local file system for deployment, to avoid deployment failures, please modify the disk discovery path to a local filesystem path that uses common filesystem formats such as ext4 or xfs.

