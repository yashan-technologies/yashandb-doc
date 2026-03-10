yaslicense是YashanDB内部使用的license生成工具。

## license gen

本命令基于数据库服务器的esn信息生成对应的license文件。

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-h, --help*        | 查看帮助信息                       |
| *-e, --esn*        | 数据库服务器的esn信息（必传参数），可在数据库所在服务器执行[yasboot cluster esn gen](yasboot/yasboot命令介绍/yasboot cluster)命令获取                         |
| *-v, --version*    | license版本，默认为v1                               |
| *-d, --deploy-type* | 数据库部署类型<br>\* SE：单机部署，默认值<br>\* CE：共享集群部署<br>\* DE：存算一体分布式集群部署                                   |
| *--expired-days*    | 过期时间（单位：天），默认为90           |
| *--cluster-version* | 数据库版本，默认为23.2                        |
| *--export-path*        | 导出路径，默认导出至执行命令时的当前目录                       |

```shell
$ yaslicense license gen -e 10F6FD3D4D1DAD04F9E2791BA458F2827D9D67745242A0B9DB6B106DE123A130
```
