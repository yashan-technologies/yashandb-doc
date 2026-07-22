## 命令参数

参数格式：

```shell
ycsctl -H
ycsctl cmd [cmd_args]
```

**-H**

显示帮助信息。

**cmd**

ycsctl工具的子命令，cmd_args为子命令的参数。

## 子命令分类

详细子命令如下表所示。

| 分类               | 子命令                 |
| ------------------ | ---------------------- |
| [集群配置命令](./集群配置命令.md)       | ycsctl create cluster  |
|                    | ycsctl set_ycr         |
|                    | ycsctl import          |
|                    | ycsctl export          |
|                    | ycsctl show config     |
|                    | ycsctl status       |
| [节点管理命令](./节点管理命令.md)       | ycsctl add node        |
|                    | ycsctl extend node     |
|                    | ycsctl modify node     |
|                    | ycsctl start ycs       |
|                    | ycsctl stop ycs        |
|                    | ycsctl start osw       |
|                    | ycsctl stop osw        |
|                    | ycsctl get             |
|                    | ycsctl set             |
|                    | ycsctl show parameter  |
| [数据库资源管理命令](./数据库资源管理命令.md) | ycsctl add database    |
|                    | ycsctl add instance    |
|                    | ycsctl start instance  |
|                    | ycsctl stop instance   |
|                    | ycsctl modify instance |
|                    | ycsctl add pdb         |
|                    | ycsctl start pdb       |
|                    | ycsctl stop pdb        |
|                    | ycsctl status pdb      |
|                    | ycsctl modify pdb      |
|                    | ycsctl remove pdb      |
| [网络资源管理命令](./网络资源管理命令.md)   | ycsctl add network     |
|                    | ycsctl modify network  |
|                    | ycsctl remove network  |
|                    | ycsctl add vip         |
|                    | ycsctl start vip       |
|                    | ycsctl stop vip        |
|                    | ycsctl relocate vip    |
|                    | ycsctl modify vip      |
|                    | ycsctl remove vip      |
|                    | ycsctl add scan        |
|                    | ycsctl start scan      |
|                    | ycsctl stop scan       |
|                    | ycsctl relocate scan   |
|                    | ycsctl modify scan     |
|                    | ycsctl remove scan     |
| [其他命令](./其他命令.md)           | ycsctl show fence         |
|                    | ycsctl query disk      |