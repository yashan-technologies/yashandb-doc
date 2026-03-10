## 命令简介

yasbak是一个针对yasrman的管理工具，对yasrman进行了简单封装，保存了执行过程中一些必须的参数。相比于yasrman，使用yasbak执行备份和恢复，可以减少参数输入次数、减轻执行备份和恢复的复杂度。

## 查看帮助

使用yasbak -h命令可查看帮助信息。

```shell
$ yasbak -h
```

## 使用指南

### yasbak deploy

本命令用于初始化yasbak和yasrman的运行环境。

本命令执行时会校验能否连接数据库，如果连接失败则初始化失败。

|  选项| 含义|
| --------------- | ------------------------------------------------------------------------------------------------------ |
| *-c,--cluster*  | 指定一个名称，该名称将用于配置数据库命名，一个数据库对应一个名称，建议与yasboot部署时的名称保持一致         |
| *-a,--addr*     | 指定数据库的yasom访问地址，yasbak将通过该地址与yasom进行通信                                              |
| *-k,--key*      | 连接yasom时校验的token，需要和yasom配置保持一致。该token值配置于yasom所在的安装目录${YASDB_HOME}/om/\<集群名>/conf/tls.toml中的rpc_secret_key参数值。         |
| *-D,--cata-log* | yasrman所使用的cata log，将指定路径生成该目录。如果未指定，则默认cata log路径规则与yasrman保持一致，如果传相对路径，则在当前工作目录下创建该目录作为cata log路径 |
| *-u,--user*     | 连接数据库使用的用户名，后续执行备份时默认将使用该用户                           |
| *-p,--password* | 连接数据库用户对应密码，该密码将通过多次加密后保存在本地配置文件内，执行clean可以清理配置            |
| *-t,--cert*     | 当yasom指定TLS加密通信时，需要指定对应的加密证书                                                          |
| *-S,--server*   | 当yasom指定server名称后，需要指定该名称                                                                   |
| *-f,--force*    | 强制初始化运行环境，指定此参数时不进行权限校验                                         |

示例

```shell
$ yasbak deploy -c yashandb -a 127.0.0.1:1675 -k 96ed7a2c90e81a9e -D ./catalog -u sys -p password
```

### yasbak run

本命令用于执行yasrman的备份、恢复、清理备份等语句。

|  选项| 含义|
| ---------------- | ------------------------------------------------------------ |
| *-c,--cluster*   | deploy时指定的cluster名称                                    |
| *-s,--sql*       | 指定yasrman运行的SQL，SQL用法参考[yasrman使用指导](../yasrman/yasrman使用指导/00yasrman使用指导) |
| *-u,--user*      | 执行yasrman使用的用户，若不指定将使用deploy时的用户名      |
| *-p,--password*  | 执行yasrman使用的密码，若不指定将使用deploy时的密码，yasbak解密后内部使用 |
| *-r,--role*      | 可选参数：primary、standby，使用指定类型节点进行备份操作   |
| *-a, --addr*     | yasdb的连接地址，若提供该参数值，将指定这个节点，--role将失效 |
| *-b,--build-all* | 在恢复备份时恢复其他备节点，指定该参数时还可以配套使用--skip-validate 和 --overwrite  |
| *--skip-validate* | 需前置指定-b，该参数才生效，指定该参数时表示跳过建立备库时的预检查，默认为检查 |
| *--overwrite* | 需前置指定-b，该参数才生效，指定该参数时表示在建立备库过程中覆盖同名文件（REDO文件和归档文件不会覆盖，需人工检查并手动清理），默认不覆盖   |

> **Note**: 
>
> 当addr和role均未指定时，节点信息选取规则如下：
>
> - 单机部署：备份时选择备节点执行操作（仅有一个节点时直接选择该节点）；恢复时选择节点列表中第一个节点执行操作。
> - 存算一体分布式集群部署：选择第一个CN进行备份或恢复。
> - 共享集群部署：选择第一个节点进行备份，选择master role节点进行恢复。

示例

yasrman提供了对接第三方XBSA API的接口，需要在SQL语句中添加PARAMS参数，PARAMS请参见[yasrman params简介](../yasrman/yasrman使用指导/00yasrman使用指导)。

```shell
# 执行备份
YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"

$ yasbak run -c yashandb -s "backup database tag 'full_01' parallelism 4 dest client params '${YASRMAN_PARAM}'"
 
# 恢复备份
$ yasbak run -c yashandb -s "restore database from tag 'full_01' dest client params '${YASRMAN_PARAM}'" -u sys -p password  --build-all
 
# 清理备份
$ yasbak run -c yashandb -s "delete backupset tag 'full_01'"
```

### yasbak reset

本命令用于清理对应数据库数据，并将数据库以nomount模式启动，可用于通过备份集恢复数据库。

|  选项| 含义|
| -------------- | ---------------------------- |
| *-c,--cluster* | deploy时指定的cluster名称    |
| *-f,--force*   | 忽略确认，直接清理            |
| *-p,--password*  | sys用户密码                 |
| *--with-arch*  | 删除归档日志                 |
| *-d, --child*   | 展示任务以及子任务信息       |
| *--disable*     | 屏蔽任务进度条展示           |
| *-w, --nowait*  | 运行后不等待执行命令结果     |

示例

```shell
$ yasbak reset -c yashandb -p password

# 全量恢复如果只恢复到备份集的时间点，需要删除归档
$ yasbak reset -c yashandb -p password --with-arch
```

### yasbak clean

本命令用于清理初始化时生成的配置文件、元数据信息等。

|  选项| 含义|
| -------------- | ------------------------------- |
| *-c,--cluster* | deploy时指定的cluster名称       |
| *-f,--force*   | 忽略确认，直接清理               |
| *-p,--purge*   | 清理时是否同时删除cata log目录   |

示例

```shell
$ yasbak clean -c yashandb --purge
```

### yasbak distribute

本命令用于分发备份集，将备份集分发到对应节点的backup目录下。

|  选项| 含义|
| -------------- | ---------------------------- |
| *-c,--cluster* | deploy时指定的cluster名称    |
| *-b,--backup-set*   | 备份集压缩包的路径            |

压缩包的格式如下：

```shell
yashandb.tar.gz     # 文件名称无约束，但文件后缀名必须为.tar.gz
    mn-1-1.tar.gz   # 压缩包命名格式为"节点类型-节点id.tar.gz"
        full_01/... # 若为增量备份，则将多个备份集全压缩在同一个压缩包中
    cn-2-1.tar.gz
        full_01/...
    dn-3-1.tar.gz
        full_01/...
```

示例

```shell
$ yasbak distribute -c yashandb -b yashandb.tar.gz
```
