## 连接信息

PDB连接信息主要包括认证信息和服务端地址。若使用驱动连接PDB，可能还涉及多地址连接。

### 认证信息

认证信息是指用于登录PDB的用户名和认证凭据。

|  格式| 说明|
|--------------------|-----------------------|
| username/password| 连接并登录数据库的用户名和密码。<br/>如果在yashan模式下创建用户时用户名未使用双引号包围，使用yasql连接时大小写不敏感。<br/>如果用户名或密码中含有OS命令相关的特殊字符（例如`@`、`/`、`.`、`!`、`$`、`'`等）需进行转义改写，详情可查阅[yasql使用指导](../../../工具手册/yasql/yasql使用指导.md)。  |

### 服务端地址

服务端地址可以为正确且唯一指向目标PDB的URL或别名。

|  信息类型| 格式| 说明|
|--------------------|-----------------------|---------------|
| URL | @host:port/pdb_name | * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../集群管理/SCAN管理.md)或[VIP](../../集群管理/VIP管理.md)，还可以使用相应的域名或IP地址。<br/>* port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。 <br/>* pdb_name：指定连接到具体某个PDB，省略则默认连接至根容器。 |
| 别名 | @别名 | 使用自定义的别名连接PDB。别名需提前在客户端所在设备的$YASDB_HOME/client/yasc_service.ini文件中定义，格式为`alias = host:port[/pdb_name]`。例如别名定义为`yashanPDB1 = 192.168.1.2:1688/pdb1`则可直接使用`@yashanPDB1`连接该地址所指向的数据库。 |


### 多地址连接

在使用驱动连接存在多个节点的PDB时，可以配置多个或多组监听地址（`host:port`）进行连接，格式为`serverType:host:port,host:port,host:port/pdb_name`，其他同[多地址连接YashanDB](../../../安装和升级/安装部署/安装后初始环境/连接数据库.md#multiaddr)。

## 连接PDB 

连接PDB的工具包括YashanDB客户端（yasql）、YashanDB驱动、YashanDB配套的崖山开发者中心YDC等。

### 前提条件

- 连接PDB时，需使用配套的v23.5.1.100及以上版本的YashanDB客户端（yasql）、YashanDB驱动等。

- PDB（mysql模式）暂未对MySQL生态周边配套工具进行兼容性验证，建议使用YashanDB客户端、YashanDB驱动等工具进行连接。

- 已安装相应版本的YashanDB客户端、YashanDB驱动，并完成环境变量配置。若在数据库所在服务器连接本地数据库，则无需重复安装yasql。

- 需确保用户已具有[CREATE SESSION权限](../../../产品安全/数据访问控制/特权与角色管理/系统特权.md)（或[CONNECT角色](../../../产品安全/数据访问控制/特权与角色管理/角色.md)）。

- 需确保发起连接的设备IP不在[黑名单](../../../产品安全/连接管理/管理IP黑白名单.md)中。

### 连接操作

本文仅介绍如何使用YashanDB客户端（yasql）连接PDB，YashanDB驱动/配套工具的连接方式将在对应文档中详细介绍。

#### 一步登录 

命令格式如下：

```shell
# 一步登录
$ yasql [-S] {认证信息}{服务端地址}  
# 常见写法如下：
$ yasql [-S] username/password@host:port[/pdb_name]
$ yasql [-S] / as sysdba
```

`- S`表示静默登录，需确保-S为首个参数，该参数不区分大小写。使用该方式登录并连接数据库不返回命令行提示、命令回显信息、登录提示信息和版本信息。

以sales用户登录PDB1示例：

```shell
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> 
```

出现SQL命令行提示符表示连接成功，若为静默登录则不会出现SQL提示符。

#### 分步登录 

命令格式如下：

```shell
# 1. 启动yasql，打开登录窗口
$ yasql /nolog

# 2. 使用connect或conn命令连接并登录数据库
SQL> conn {认证信息}{服务端地址}
```

以sales用户登录PDB1示例：

```shell
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {version_number} x86_64
SQL> conn sales/********@192.168.1.2:1688/pdb1

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL>
```

### 退出登录 

命令格式如下：

```sql
exit
```
