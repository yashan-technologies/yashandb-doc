## package se gen

本命令用于根据输入的各选项生成单机部署的配置文件。

|  选项| 含义|
| --------------------- |-----------------------------------------------------------|
| **指定服务器信息** |  |
| *-u,--username*       | 服务器SSH用户名                  |
| *--groupname*         | 用户组名称，默认和`username`相同                  |
| *-p,--password*       | SSH登录密码          |
| *-N,--no-password*    | SSH免密登录             |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值<br />用于执行需要sudo权限的命令，例如创建cgroup目录 |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值      |
| *--host*              | 服务器连接信息表达式           |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开        |
| *--port*              | 服务器SSH连接端口              |
| **数据库基础信息配置** |  |
| -c,--cluster     | 规划的集群名称（必传参数），名称必须以字母开头，长度2 ~ 63个字符，可包含数字和大部分可见ASCII字符（除`,`、`'`、`"`、`+`、`-`、`*`、`/`、`|`、`(`、`)`、`@`、`[`、`]`、`{`、`}`、`~`、`` ` ``、`%`、`:`、`?`、`.`、空格以及控制字符`=`、`!`、`<`、`>`、`;`、`&`、`^`外） |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的数据目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | 运行日志目录，存放数据库的run.log和slow.log、yasom和yasagent的日志                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）              |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP|
| *--create-cgroup*     | 是否创建资源管理cgroup目录，默认不创建。创建需使用sudo权限，要求至少指定-u，-p或者-su，-sp其中之一     |
| *--cgroup-path*       | 自定义指定资源管理cgroup目录，不指定时默认为`/sys/fs/cgroup`|
| *-m, --mode*          | 数据库的语法模式，可选项[yashan,mysql]<br/>* yashan：表示部署为yashan模式，省略时默认为该值，以yashan模式安装后无法直接切换至mysql模式<br/>* mysql：表示部署为mysql模式     |
| **节点规模** |  |
| *--node*              | 单机DB组及节点部署的节点规模，默认为1             |
| *--cascade-node*      | 级联备节点个数 |
| *--cascade-parent*    | 级联备库需绑定的父级备库的节点ID                   |
| *--group*             | 仅在进行双复制组主备部署时使用，指定复制组的个数，默认为1                  |
| *--standby-node*      | 仅在进行双复制组主备部署时使用，指定复制组中的备节点数                    |
| **数据库性能相关** | 若安装时不指定此类参数，安装完成后可按需通过调用[DBMS_PARAM](../../../开发手册/PL参考手册/内置高级包/DBMS_PARAM.md).OPTIMIZE进行调整 |
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器可供数据库使用内存百分比上限，默认为80，可用量上限计算公式为`服务器总内存 * memory-limit`<br />须配合--recommend-param使用 |
| *--cpu-limit*         | 服务器可供数据库使用CPU百分比上限，默认为100，可用量上限计算公式为`服务器总CPU * cpu-limit`<br />须配合--recommend-param使用  |
| *--scene*             | 参数调优的场景，默认为NORMAL且只能为NORMAL<br />须配合--recommend-param使用 |
| *--scale-factor*      | 测试场景的数据量（单位：GB），默认为100<br />须配合--recommend-param使用 |
| **其他参数** |  |
| *-h,--help*        | 查看当前命令的帮助信息  |
| *--ssl-protocol*      | 指定配置数据库[客户端<->服务器可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)的协议，目前仅支持SSL <br/> 须配合--ssl-path使用   |
| *--ssl-path*          | 指定SSL相关证书的存放路径，须配合--ssl-protocol使用 <br/> 执行数据库安装时，会将该路径下的所有文件全部拷贝至每台服务器的$YASDB_HOME/ssl路径下                    |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组<br>**不推荐使用**，指定后安装用户将无法通过[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)免密登录数据库          |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署|
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，IP默认为127.0.0.1     |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4            |
| *-o, --output*        | 配置文件的输出路径  |
| *--dev*               | 仅内部开发使用  |
| *--plugins*           | 需要安装的插件，多个选项间使用逗号`,`分隔，可选项及其含义如下：<br>* all：安装所有插件，默认值<br>* none：不安装插件<br>* dblink：安装用于DBLink相关功能的插件<br>* gis：安装用于内置[GIS](../../../开发手册/SQL参考手册/内置函数/GIS Function/00GIS Function)函数的插件<br>* listagg：安装用于内置[LSFA_LISTAGG](../../../开发手册/SQL参考手册/内置函数/LSFA_LISTAGG)函数的插件<br>* s3：安装用于S3 bucket相关功能的插件<br>* udf：安装用于[自定义函数](../../../开发手册/PL参考手册/PL对象/自定义函数)相关功能的插件<br>* xml：安装用于内置[XML](../../../开发手册/SQL参考手册/内置函数/XML Function/00XML Function)函数的插件 |


<span id="ce" name="ce"></span>

## package ce gen

本命令用于根据输入的各选项生成共享集群部署或分布式集群部署的配置文件。


|  选项| 含义|
| --------------------- |----------------------------------------------------------|
| **指定服务器信息** |  |
| *-u,--username*       | 服务器SSH用户名                  |
| *--groupname*         | 用户组名称，默认和`username`相同                  |
| *-p,--password*       | SSH登录密码          |
| *-N,--no-password*    | SSH免密登录             |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值<br />用于执行需要sudo权限的命令，例如创建cgroup目录 |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值      |
| *--host*              | 服务器连接信息表达式           |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开        |
| *--port*              | 服务器SSH连接端口       |
| *--cn-ip*                | 分布式集群部署的计算节点IP地址，多个IP间用逗号`,`隔开            |
| *--dn-ip*                | 分布式集群部署的数据节点IP地址，多个IP间用逗号`,`隔开            |
| **数据库基础信息配置** |  |
| -c,--cluster     | 为数据库环境规划的集群名称（必传参数），名称必须以字母开头，长度4~64个字符，只能包含数字、字母和下划线 |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的数据目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | 运行日志目录，存放数据库的run.log和slow.log、yasom和yasagent的日志                 |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）          |
| *--data*              | 存储网络中的数据盘，多个数据盘信息间用逗号`,`隔开         |
| *-fg, --failgroup*    | 共享集群/分布式集群磁盘组的故障组数量（默认为1）              |
| *--disk-found-path*   | 磁盘发现路径（默认为/dev/yfs）          |
| *--system-data*       | 存储网络中的系统盘，多个系统盘信息间用逗号`,`隔开       |
| *--inter-cidr*        | 共享集群/分布式集群单个集群内实例之间的网络通信链路地址（默认与IP网段一致）|
| *--public-network*              | 共享集群对外提供服务的公网，格式为子网/子网掩码[/网卡名]，网卡名可以省略，例如：192.168.1.0/24   |
| *--vips*              | 共享集群的VIP配置信息，格式为IP地址/子网掩码[/网卡名]，例如：192.168.1.62/255.255.255.0/ens192，如果无法确保同一集群中所有服务器访问公网的网卡名一致，则必须省略网卡名。需配合--public-network参数使用，且IP地址应属于公网网段。VIP个数与节点个数一致，多个VIP配置之间用逗号`,`隔开     |
| *--scanname*              | 共享集群的SCAN域名，需配合--public-network参数使用    |
| *--trtype*               | 分布式集群部署中的NVMe通信协议方式，支持指定为rdma或tcp |
| **节点规模** |  |
| *--node*              | * 共享集群部署时，表示单个集群中的实例数量，默认为2<br/>* 分布式集群部署时，表示单个集群中的CN节点数量，默认为2，CN节点会创建在--cn-ip指定的服务器上 |
| *--group*             | 共享集群组的个数，默认数量为1                  |
| *--standby-node*      | 主备集群部署时，备集群中的实例数量，默认为2       |
| **数据库性能相关** | 若安装时不指定此类参数，安装完成后可按需通过调用[DBMS_PARAM](../../../开发手册/PL参考手册/内置高级包/DBMS_PARAM.md).OPTIMIZE进行调整 |
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器可供数据库使用内存百分比上限，默认为80，可用量上限计算公式为`服务器总内存 * memory-limit`<br />须配合--recommend-param使用 |
| *--cpu-limit*         | 服务器可供数据库使用CPU百分比上限，默认为100，可用量上限计算公式为`服务器总CPU * cpu-limit`<br />须配合--recommend-param使用  ||
| *--scene*             | 参数调优的场景，默认为NORMAL且只能为NORMAL<br />须配合--recommend-param使用 |
| *--scale-factor*      | 测试场景的数据量（单位：GB），默认为100<br />须配合--recommend-param使用 |
| **其他参数** |  |
| *-h,--help*        | 查看当前命令的帮助信息  |
| *--ssl-protocol*      | 指定配置数据库[客户端<->服务器可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)的协议，目前仅支持SSL <br/> 须配合--ssl-path使用  |
| *--ssl-path*          | 指定SSL相关证书的存放路径，须配合--ssl-protocol使用 <br/> 执行数据库安装时，会将该路径下的所有文件全部拷贝至每台服务器的$YASDB_HOME/ssl路径下                   |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组<br>**不推荐使用**，指定后安装用户将无法通过[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)免密登录数据库         |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署|
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，IP默认为127.0.0.1     |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4            |
| *-o, --output*        | 配置文件的输出路径  |
| *--dev*               | 仅内部开发使用  |
| *--plugins*           | 需要安装的插件，多个选项间使用逗号`,`分隔，可选项及其含义如下：<br>* all：安装所有插件，默认值<br>* none：不安装插件<br>* dblink：安装用于DBLink相关功能的插件<br>* gis：安装用于内置[GIS](../../../开发手册/SQL参考手册/内置函数/GIS Function/00GIS Function)函数的插件<br>* listagg：安装用于内置[LSFA_LISTAGG](../../../开发手册/SQL参考手册/内置函数/LSFA_LISTAGG)函数的插件（对应功能暂不适用于共享集群/分布式集群部署，无需单独指定安装该插件）<br>* s3：安装用于S3 bucket相关功能的插件（对应功能暂不适用于共享集群/分布式集群部署，无需单独指定安装该插件）<br>* udf：安装用于[自定义函数](../../../开发手册/PL参考手册/PL对象/自定义函数)相关功能的插件<br>* xml：安装用于内置[XML](../../../开发手册/SQL参考手册/内置函数/XML Function/00XML Function)函数的插件 |

<span id="de" name="de"></span>

## package de gen

本命令用于根据输入的各选项生成存算一体分布式集群部署的配置文件。


|  选项| 含义|
| --------------------- |-----------------------------------------------------------|
| **指定服务器信息** |  |
| *-u,--username*       | 服务器SSH用户名                  |
| *--groupname*         | 用户组名称，默认和`username`相同                  |
| *-p,--password*       | SSH登录密码          |
| *-N,--no-password*    | SSH免密登录             |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值<br />用于执行需要sudo权限的命令，例如创建cgroup目录 |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值      |
| *--host*              | 服务器连接信息表达式           |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开        |
| *--port*              | 服务器SSH连接端口       |
| **数据库基础信息配置** |  |
| -c,--cluster     | 规划的集群名称（必传参数），名称必须以字母开头，长度2 ~ 63个字符，可包含数字和大部分可见ASCII字符（除`,`、`'`、`"`、`+`、`-`、`*`、`/`、`|`、`(`、`)`、`@`、`[`、`]`、`{`、`}`、`~`、`` ` ``、`%`、`:`、`?`、`.`、空格以及控制字符`=`、`!`、`<`、`>`、`;`、`&`、`^`外） |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）         |
| *--data-path*         | 数据库实例的数据目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）     |
| *--log-path*          | 运行日志目录，存放数据库的run.log和slow.log、yasom和yasagent的日志                  |
| *--begin-port*        | 起始端口，默认为1688|
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）              |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）          |
| *--din-cidr*          | 分布式网络通信链路地址（默认与IP网段一致）        |
| *--table-type*        | 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为LSC                       |
| *--create-cgroup*     | 是否创建资源管理cgroup目录，默认不创建。创建需使用sudo权限，要求至少指定-u，-p或者-su，-sp其中之一     |
| *--cgroup-path*       | 自定义指定资源管理cgroup目录，不指定时默认为`/sys/fs/cgroup`|
| **节点规模** |  |
| *-d, --deploy-mode*   | 存算一体分布式集群部署的规模模式：<br>\* MINI：最小规模部署，按1MN 1CN 3DN规模部署在同一台服务器<br>\* NORMAL：常规部署，根据下述节点规模进行部署               |
| *--mn*                | 分布式mn类型节点的部署规模，默认为1           |
| *--cn*                | 分布式cn类型节点的部署规模，默认为1           |
| *--dn*                | 分布式dn组及节点规模，例如1-3表示1一个组3个节点（组内一主两备）                    |
| **数据库性能相关** | 若安装时不指定此类参数，安装完成后可按需通过调用[DBMS_PARAM](../../../开发手册/PL参考手册/内置高级包/DBMS_PARAM.md).OPTIMIZE进行调整 |
| *--recommend-param*   | 是否开启推荐参数功能                       |
| *--memory-limit*      | 服务器可供数据库使用内存百分比上限，默认为80，可用量上限计算公式为`服务器总内存 * memory-limit`<br />须配合--recommend-param使用 |
| *--cpu-limit*         | 服务器可供数据库使用CPU百分比上限，默认为100，可用量上限计算公式为`服务器总CPU * cpu-limit`<br />须配合--recommend-param使用  |
| *--scene*             | 参数调优的场景，可选[NORMAL,BATCH,TPCH]，默认为NORMAL。须配合--recommend-param使用<br>- NORMAL：通用混合负载场景，适用于所有表类型 <br>- BATCH：批量处理场景，仅当表类型为HEAP时可设置为BATCH。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优<br />- TPCH：复杂分析查询场景，仅当表类型为LSC时可设置为TPCH。使用TPCH场景进行调优推荐时，必须确保所有节点都执行参数推荐，否则可能重启时可能会因为连接参数不匹配而导致失败 |
| *--scale-factor*      | 测试场景的数据量（单位：GB），默认为100<br />须配合--recommend-param使用 |
| **其他参数** |  |
| *-h,--help*        | 查看当前命令的帮助信息  |
| *--ssl-protocol*      | 指定配置数据库[客户端<->服务器可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)的协议，目前仅支持SSL <br/> 须配合--ssl-path使用   |
| *--ssl-path*          | 指定SSL相关证书的存放路径，须配合--ssl-protocol使用 <br/> 执行数据库安装时，会将该路径下的所有文件全部拷贝至每台服务器的$YASDB_HOME/ssl路径下                    |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组<br>**不推荐使用**，指定后安装用户将无法通过[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)免密登录数据库          |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署|
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，IP默认为127.0.0.1     |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4            |
| *-o, --output*        | 配置文件的输出路径  |
| *--dev*               | 仅内部开发使用  |
| *--node-info*         | 指定节点部署信息的文件路径，该参数用于将备份集恢复到空集群，通过yasrman的[查看备份集](../../yasrman/yasrman使用指导/00yasrman使用指导.md)获得。     |
| *--plugins*           | 需要安装的插件，多个选项间使用逗号`,`分隔，可选项及其含义如下：<br>* all：安装所有插件，默认值<br>* none：不安装插件<br>* dblink：安装用于DBLink相关功能的插件<br>* gis：安装用于内置[GIS](../../../开发手册/SQL参考手册/内置函数/GIS Function/00GIS Function)函数的插件<br>* listagg：安装用于内置[LSFA_LISTAGG](../../../开发手册/SQL参考手册/内置函数/LSFA_LISTAGG)函数的插件（对应功能暂不适用于存算一体分布式集群部署，无需单独指定安装该插件）<br>* s3：安装用于S3 bucket相关功能的插件<br>* udf：安装用于[自定义函数](../../../开发手册/PL参考手册/PL对象/自定义函数)相关功能的插件（对应功能暂不适用于存算一体分布式集群部署，无需单独指定安装该插件）<br>* xml：安装用于内置[XML](../../../开发手册/SQL参考手册/内置函数/XML Function/00XML Function)函数的插件（对应功能暂不适用于存算一体分布式集群部署，无需单独指定安装该插件） |

## package config join-demo

本命令用于生成托管命令使用的配置文件模板join_demo.toml。

|  选项| 含义|
| ------------ | ----------------------------------- |
| *-t, --type* | 待托管数据库集群的类型（必传参数），SE表示单机部署，DE表示存算一体分布式集群部署 |
| *-d, --dir*  | 配置文件生成的路径（默认当前目录）  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package config join-demo -t SE
```

## package config show

本命令用于展示生成的集群配置文件信息。

|  选项| 含义|
|-----------------| ------------------------------- |
| *-c, --cluster* | 部署YashanDB的集群名（隐藏参数）         |
| *-t, --toml*    | 需要展示的集群配置文件名（例如yashandb.toml）（隐藏参数） |
| *-b, --by*      | 按服务器/节点组来展示，默认值为host（隐藏参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package config show -c yashandb
```

## package upload

本命令用于更新hosts.toml的信息和上传新的YashanDB软件包。

|  选项| 含义|
| ------------------- | -------------------------- |
| *-t, --toml*        | 待更新的toml文件的路径（必传参数） |
| *-i, --install-pkg* | 新的安装包路径（废弃参数）             |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package upload -t hosts.toml
```

## package install

本命令用于对远程服务器执行软件包安装（可通过toml文件配置对全部还是特定服务器执行安装），软件包为tar.gz格式。

|  选项| 含义|
| -------------------- | ------------------------------------ |
| *\-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数） |
| *-f, --force*        | 忽略错误并强制安装，默认为false      |
| *--disable*          | 屏蔽任务进度条展示                   |
| *--deps*             | 依赖包文件本地路径（隐藏参数）                 |
| *\-i, --install-pkg* | 软件包文件本地路径（废弃参数）       |
| *--plugin*           | 插件包文件本地路径（废弃参数）     |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package install -t hosts.toml
```

## package uninstall

本命令用于对远程服务器上已安装的软件进行卸载（可通过toml文件配置选对全部还是对特定服务器执行卸载）。

|  选项| 含义|
| ---------------- | ------------------------------------------- |
| *\-c, --cluster* | 部署YashanDB的集群名（必传参数）            |
| *-t, --toml*    | 以SSH方式卸载需要指定服务器配置文件`hosts.toml`的路径         |
| *-d, --child*    | 展示任务以及子任务信息                      |
| *-f, --force*    | 强制卸载，默认false|
| *--disable*      | 屏蔽任务进度条展示 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 卸载整个集群
$ yasboot package uninstall -c yashandb

# 按配置文件卸载
$ yasboot package uninstall -c yashandb -t hosts.toml
```

## package upgrade

本命令用于对远程服务器上已安装的软件（yasom，yasagent）进行升级。
必须在升级数据库完成后才能执行。

|  选项| 含义|
| ------------- | ---------------------------------------- |
| *\-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数） |
| *--package*   | 升级软件包的绝对路径（废弃参数）         |
| *--plugin*    | 插件包文件本地路径（废弃参数）        |
| *-d, --child* | 展示任务以及子任务信息                   |
| *--disable*   | 屏蔽任务进度条展示                       |
| *--deps*      | 依赖包文件本地路径（隐藏参数）        |
| *--same-version*    | 同版本升级（隐藏参数）        |
| *--only-replace-without-version-limit*    | 忽略检查直接替换二进制，该参数属于高危操作，**请勿在生产环境使用**（隐藏参数）  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package upgrade -t hosts.toml
```

## package rollback

本命令用于对远程服务器上已安装的软件（yasom，yasagent）进行回滚。

|  选项| 含义|
| ---------------- | ---------------------------------------- |
| *\-c, --cluster* | 部署YashanDB的集群名（必传参数）         |
| *\-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数） |
| *--disable*      | 屏蔽任务进度条展示                       |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package rollback -c yashandb -t hosts.toml
```

## package verify

本命令用于校验安装包是否被篡改。

示例

```shell
# 解压安装包后，使用安装包里的yasboot执行如下命令即可
$ yasboot package verify
```

## package host

本命令用于修改服务器配置文件下的配置信息，目前只可以修改password。

|  选项| 含义|
|-----------------|-------------------------------------|
| *-t, --toml*    | 待修改信息的服务器配置文件（例如hosts.toml）的路径（必填参数） |
| *-k, --key*     | 修改的配置名称（必填参数）                       |
| *-v, --value*   | 修改的配置数值（必填参数）                |
| *--host-id*     | 需要修改信息的服务器ID（隐藏参数，不填表示所有）                    |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot package host -t hosts.toml -k password -v 123 --host-id host0001
```


## package config gen

本命令用于根据输入的各选项生成配置文件。

该命令下个大版本将不再维护，请换用package se/de/ce gen命令。

|  选项| 含义|
| --------------------- | ------------------------------------------- |
| *\-c,--cluster*       | 规划的集群名称（必传参数）           |
| *-u,--username*       | 服务器SSH用户名|
| *--groupname*         | 用户组名称，默认和`username`相同      |
| *-p,--password*       | SSH登录密码  |
| *-N,--no-password*    | SSH免密登录  |
| *--ip*                | 服务器SSH的IP地址，多个IP间用逗号`,`隔开                      |
| *--port*              | 服务器SSH连接端口                       |
| *-i, --install-path*  | 数据库安装路径，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）                       |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值（用于执行需要sudo权限的命令，例如创建cgroup目录）                   |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值             |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组          |
| *--boot-start-monit*    | 开启自启动monit守护进程（添加启动命令到/etc/rc.local）         |
| *--host*              | 服务器连接信息表达式                    |
| *-t, --yas-type*      | 数据库的部署形态：<br>\* SE：单机部署<br>\* CE：共享集群/分布式集群部署<br>\* DE：存算一体分布式集群部署，默认值 |
| *-d, --deploy-mode*   | 存算一体分布式集群部署的部署规模：<br>\* MINI：最小规模部署，按1MN 1CN 3DN规模部署在同一台服务器<br>\* NORMAL：常规部署，根据下述节点规模进行部署 |
| *-L,--local*          | 是否本地部署（不需要安装包），默认为false，ip默认为127.0.0.1                     |
| *--ipv6*              | 本地部署是否使用IPv6，默认使用IPv4    |
| *--mn*                | 存算一体分布式集群部署中MN组内节点的部署规模，默认为1   |
| *--cn*                | 存算一体分布式集群部署中CN节点的部署规模，默认为1   |
| *--dn*                | 存算一体分布式集群部署中DN组及节点规模，例如1-3表示1一个组3个节点（组内一主两备）|
| *--db*                | 单机DB组及节点部署的节点规模，默认为1 |
| *--listen-cidr*       | 数据库的监听地址（默认与IP网段一致）  |
| *--din-cidr*          | 分布式网络通信链路地址（默认与IP网段一致）                     |
| *--replica-cidr*      | 主备复制链路地址（默认与IP网段一致）  |
| *--begin-port*        | 起始端口1688 |
| *--data-path*         | 数据库实例的DATA目录，支持数字、字母（区分大小写）以及部分符号（`/`、`-`、`_`、`.`）                 |
| *-f, --force*         | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署               |
| *-o, --output*        | 文件输出路径               |
| *--ce*                | 共享集群类型节点的部署规模，默认为2，最大为8         |
| *--inter-cidr*        | 共享集群/分布式集群内部网络通信链路地址（默认与IP网段一致）               |
| *--ce-data*           | 共享集群/分布式集群数据盘，多个数据盘信息间用逗号`,`隔开                       |
| *--create-cgroup*     | 是否创建资源管理cgroup目录，默认不创建。创建需使用sudo权限，要求至少指定-u，-p或者-su，-sp其中之一     |
| *--cgroup-path*       | 自定义指定资源管理cgroup目录，不指定时默认为`/sys/fs/cgroup`|
| *--recommend-param*   | 是否开启推荐参数功能                  |
| *--memory-limit*      | 服务器的可使用内存百分比上限，默认为80            |
| *--table-type*        | 使数据库配置参数更适合主要业务的表类型，可选[HEAP,TAC,LSC]，单机部署中默认为HEAP，存算一体分布式集群部署中默认为LSC          |
| *-fg, --failgroup*    | 共享集群/分布式集群磁盘组的故障组数量（默认为1）          |
| *--ce-group*          | 共享集群/分布式集群组的个数，共享集群/分布式集群部署中的独有参数，默认数量为1          |
| *--disk-found-path*   | 共享集群/分布式集群磁盘发现路径（默认为/dev/yfs）                      |

参数说明：

- IP：可以采用`192.168.157.[1-3]`缩写表示`192.168.157.1, 192.168.157.2, 192.168.157.3`三个连续的IP地址。若为IPv6地址需要带中括号，若为链路本地地址还需带网卡号。IP支持配置为如下四种组合：
    - IPv4地址
    - IPv6地址
    - IPv4地址与IPv4映射IPv6地址（`[::ffff:192.168.157.1]`）
    - IPv6地址与IPv4映射IPv6地址
- CIDR：可以通过`--listen-cidr 0.0.0.0/0,192.168.1.0/22`配置多个网段。
- 密码：若已配置SSH免密，密码为可选参数。

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

上述命令等同于：

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

使用服务器连接信息表达式：

- 全参数：`username:password@IP1,IP2,IP3:port:install-path`
- 配置了SSH免密的情况下password可以为空：`username:@IP1,IP2,IP3:port:install-path`
- IP支持通过缩写表示连续IP地址：`username:@192.168.157.[1-3]:port:install-path`
- 端口可以省略：`username:password@IP1,IP2,IP3:install-path`
- 安装路径可以省略：`username:password@IP1,IP2,IP3:port`

```shell
$ yasboot package config gen -c yashandb \
--host yashan:password@IP1,IP2,...,IPn:22:/var/database/yashan --begin-port 1688
```
- 磁盘发现路径：当使用本地文件系统部署时，为避免部署失败，请修改磁盘发现路径到一个使用ext4或xfs等常见文件系统格式的本地文件系统路径上。

