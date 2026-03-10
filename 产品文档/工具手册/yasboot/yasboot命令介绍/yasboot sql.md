yasboot集成了yasql工具的相关功能，用于打开yasql客户端，或者直接在命令行执行SQL。

| 选项             | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| *-c,--cluster*   | YashanDB的集群名                                             |
| *-n, --node-id*  | 部署的集群中的节点id，例如 1-1                               |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password* | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码                         |
| *-d, --dsn*      | 提供以一串字符串执行账号密码输入的方式，例如 'username/password@127.0.0.1:1688' |
| *-e, --echo*     | 将sql文件执行过程输出到控制台                                |
| *-s, --sql*      | 执行单条SQL命令并且退出                                    |
| *-f, --file*     | 支持导入sql文件并执行                                        |

示例1（连接yasql客户端）

```shell
# 指定节点标识连接
$ yasboot sql -n 1-1 -u username -p password -c yashandb

# 指定IP:PORT连接
$ yasboot sql -d username/password@127.0.0.1:1688

# IPv6需要添加中括号
$ yasboot sql -d username/password@[::1]:1688
```

示例2（执行SQL语句）

```shell
$ yasboot sql -n 1-1 -u username -p password -c yashandb -s 'select status from v$instance;'

STATUS            
----------------- 
OPEN             

```

示例3（执行SQL文件）

```shell
$ yasboot sql -d username/password@127.0.0.1:1678 -f get_status.sql --echo
```
