## dataspace redistribute

本命令用于分布式DN节点组数据手动进行重分布。

| 选项             | 含义                            |
| ---------------- |-------------------------------|
| *-c, --cluster*  | YashanDB的集群名（必传参数）            |
| *--dataspace-id*    | dataspace id（route$中的DS_ID），默认为0  |
| *--target-group* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字串） |
| *--chunk-id*      | chunkId（route$中的CHUNK）        |
| *--clean-residual-immediately*      | 是否自动清理chunk表空间                |
| *--disable*     | 屏蔽运行的进度信息                     |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *-d,--child*          | 展示包含子任务执行信息                                       |

示例

```shell
$ yasboot dataspace redistribute -c yashandb
$ yasboot dataspace redistribute -c yashandb --chunk-id 1 --target-group 4
```
