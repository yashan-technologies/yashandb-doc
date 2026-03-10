连接YashanDB服务端（mysql模式）的工具除了YashanDB客户端（yasql）、YashanDB JDBC驱动、YashanDB C驱动外，还支持MySQL的生态周边配套工具，例如MySQL客户端、MySQL Connector/J等。

本章节仅介绍通过YashanDB客户端（yasql）和MySQL客户端连接崖山数据库。

当通过驱动连接数据库时，可参考[JDBC驱动](快速入门/00快速入门)和[C驱动](../全部手册/开发手册/C语言系驱动/C驱动/00C驱动)了解详细的连接方式。

> **Note**: 
>
> 以mysql模式安装后，在数据库OPEN阶段新建的所有会话默认为mysql模式，在NOMOUNT或MOUNT阶段创建的会话则仍为yashan模式。

## 使用yasql连接

### 连接信息

YashanDB服务端的连接信息主要包括认证信息和服务端地址，若使用驱动连接，还涉及多IP场景的连接模式。

#### 认证信息

认证信息是指用于登录YashanDB的用户名和认证凭据。

|  格式| 说明|
|--------------------|-----------------------|
| username/password| 连接并登录数据库的用户名和密码。<br/>yashan模式下创建用户时用户名未使用双引号包围，使用yasql连接时大小写不敏感；mysql模式下大小写敏感，使用yasql工具连接时用户名需使用双引号包围。<br/>如果用户名或密码中含有特殊符号（下划线除外），可能在输入命令时需要对特殊符号进行一定规则的改写，详情可查阅[yasql使用指导](../全部手册/工具手册/yasql/yasql使用指导)。  |
| / as sysdba| 在数据库服务端所在服务器上通过[操作系统认证](../全部手册/产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)方式登录时，可以省略用户名和密码，但不能省略/，且需声明as sysdba。  |

#### 服务端地址

服务端地址可以为URL或别名，需正确且唯一指向目标服务端的监听地址（即yashandb.toml配置文件中的LISTEN_ADDR）。

|  信息类型| 格式| 说明|
|--------------------|-----------------------|---------------|
| URL | @host:port<br/>或<br/>直接省略 | <br/>* host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。<br/>* port：数据库服务端监听端口，如安装过程中未进行调整，yashan模式默认端口为1688，mysql模式且通过MySQL客户端或第三方工具连接时默认端口为1690。<br/>* 直接省略（即无URL）：表示连接到本地YASDB_DATA环境变量对应的数据库实例。 |
| 别名 | @别名 | 表示使用在$YASDB_HOME/client/yasc_service.ini中定义的别名进行连接，别名定义示例：`local = 127.0.0.1:1690`。 |

#### 连接模式（多IP场景）

连接模式（多IP场景）仅在使用驱动连接YashanDB服务端时可以进行配置，mysql模式仅支持[JDBC驱动](../全部手册/开发手册/JDBC驱动/JDBC驱动使用介绍/00JDBC驱动使用介绍)和[C驱动](../全部手册/开发手册/C语言系驱动/C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect)，且要求YashanDB服务端存在多个节点。

|  连接模式| 格式| 说明|
|--------------------|-----------------------|---------------|
| primary模式 | PRIMARY:host1:port,host2:port,……,hostn:port<br/>（`PRIMARY:`可省略） | 轮询连接高可用部署中的主库节点 |
| standby模式 |STANDBY:host1:port,host2:port,……,hostn:port | 轮询连接高可用部署中的备库节点 |
| loadBalance模式 | LOADBALANCE:host1:port,host2:port,……,hostn:port| 连接最优节点（连接数最少视为最优）|
|primaryLoadBalance模式| PRIMARYLOADBALANCE:host1:port,host2:port,……,hostn:port |连接最优主库节点|
|standbyLoadBalance模式| STANDBYLOADBALANCE:host1:port,host2:port,……,hostn:port |负载均衡连接最优备库节点 |

### 前提条件

- 已完成[YashanDB客户端安装](../全部手册/安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)和环境变量配置，如在服务端所在服务器的安装用户下连接本地数据库，则无需重复安装yasql。

- 需确保发起连接的设备IP不在[黑名单](../全部手册/产品安全/连接管理/管理IP黑白名单)中。

### 连接操作

为正确将用户名传递至数据库服务端，使用yasql以mysql模式下创建的用户连接YashanDB服务端（mysql模式）时需要使用双引号将用户名包围。

- **分步登录**

    命令格式如下：

    ```shell
    # 1. 启动yasql，打开登录窗口
    $ yasql /nolog

    # 2. 使用connect或conn命令连接并登录数据库
    SQL> conn {认证信息}{服务端地址}
    ```

    以sales用户登录示例：

    ```shell
    $ yasql /nolog
    YashanDB SQL Enterprise Edition Release {version_number} x86_64
    SQL> conn "sales"/********@192.168.1.2:1688

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL>
    ```

- **一键登录**

    命令格式如下：

    ```shell
    # 一步登录
    $ yasql [-S] {认证信息}{服务端地址}  
    # 常见写法如下：
    $ yasql [-S] username/password@host:port  
    $ yasql [-S] / as sysdba
    ```

    以sales用户登录示例：

    ```shell
    $ yasql \"sales\"/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    出现SQL命令行提示符表示连接成功，若为静默登录则不会出现SQL提示符。

- **退出登录**

    ```sql
    exit
    ```

## 使用MySQL客户端连接

使用MySQL客户端连接YashanDB（mysql模式）时，无法使用超级管理员sys登录。

当使用第三方工具连接YashanDB（mysql模式）时，请参考[通过navicat连接YashanDB](快速入门/使用SQL语言进行数据库操作)。

### 连接信息

- 认证信息：使用除sys外的数据库用户连接数据库服务。

- 服务端地址：使用MySQL的生态周边配套工具连接YashanDB时默认使用MySQL协议的监听地址（即yashandb.toml配置文件中的mysql_addr）。

### 前提条件

- 已完成MySQL客户端安装和环境变量配置。

- 已创建普通用户。

- 需确保发起连接的设备IP不在[黑名单](../全部手册/产品安全/连接管理/管理IP黑白名单)中。

### 连接操作

- **登录**

    登录命令格式如下：

    ```shell
    $ mysql -h {host} -P {port} -u {username} -p{password} [-D {database_name}]
    ```

    参数介绍：

    - **-h {host}**：数据库所在服务器的IP地址。

    - **-P {port}**：数据库服务端MySQL协议的监听端口（即yashandb.toml配置文件中mysql_addr的端口部分），如安装过程中未进行调整，默认为1690。

    - **-u {username}**：数据库用户名。

    - **-p{password}**：用户名对应的登录密码。

    - **{database_name}**：可选项，指定连接YashanDB后当前会话中后续操作的默认数据库。如不指定，则不进入任何数据库，连接后可通过[USE](SQL参考手册/SQL语句/USE)语句进行指定。

    以sales用户登录示例：

    ```shell
    $ mysql -h 192.168.1.2 -P 1690 -u sales -p********

    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 15
    Server version: {版本信息}

    ……

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql>
    ```

- **退出登录**

    ```sql
    exit
    ```

## 修改MySQL服务监听信息

部署YashanDB单机、单机主备并配置为mysql模式时，服务进程启动后默认拉起MySQL监听服务，MySQL监听服务将安装部署过程中yashandb.toml的mysql_addr参数作为服务监听端口，用于监听通过MySQL协议发起的连接请求。

MySQL监听服务，采用插件式服务方式启动。即数据库基本服务的运行，不依赖插件服务。插件服务作为可选的服务，通过配置文件来配置在数据库实例启动时是否加载。

### 配置文件说明

- 文件路径：$YASDB_DATA/config/service.ini

- 文件格式：每个插件式服务在配置文件中独占一行，最多支持配置9个服务，通过SERVICEn表示不同服务，编号n的取值范围为[0,8]。

- mysql监听服务配置格式：SERVICE1 = {library = yas_my, name = mysql, args = "URL=127.0.0.1:1279,RSA_PRIVATE_FILE=$YASDB_DATA/mysqlkey/private_key.pem,RSA_PUBLIC_FILE=$YASDB_DATA/mysqlkey/public_key.pem"}

- 配置项含义如下：

    | 配置项| 配置项描述| 取值范围|
    | ------------ | -------- | --------- |
    | library | 提供该插件服务的二进制库地址 | yas_my |
    | name | 插件服务的名称|用户自定义，最大64字节 |
    | args | 每个插件服务特有的参数，多个参数间用逗号`,`分隔 | 根据插件服务可接受的参数及格式配置 |

    args中可传入的MySQL监听服务配置参数如下：

    | 参数名| 参数描述| 取值范围|
    | ------------ | -------- | --------- |
    | URL | MySQL监听服务地址 | IP:Port |
    | IPC | MySQL IPC通信监听地址|socket文件绝对路径 |
    | RSA_PRIVATE_FILE | RSA密码私钥路径 | 绝对路径 |
    | RSA_PUBLIC_FILE | RSA密码公钥路径 | 绝对路径 |

    RSA密码的公钥和私钥可采用OpenSSL工具生成：

    ```shell
    # 私钥文件
    $ openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
    
    # 公钥文件
    $ openssl rsa -pubout -in private_key.pem -out public_key.pem
    ```

### 生效方式

修改service.ini文件需重启数据库生效。
