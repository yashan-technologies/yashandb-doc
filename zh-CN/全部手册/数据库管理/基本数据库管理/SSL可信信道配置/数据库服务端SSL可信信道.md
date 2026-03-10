YashanDB启用SSL可信信道连接要求由服务器生成根证书、服务器证书和DH文件，客户端获取服务器的根证书，在通讯时进行客户端到服务端的安全验证。

> **Caution**: 
>
> - 一旦服务器开启SSL可信信道，所有的客户端都必须有根证书才能连接到数据库。
>
> - 启用SSL可信信道和用户密码认证无关联，用户登录数据库仍需输入密码。

## 服务端配置

### 工具准备

生成证书需使用OpenSSL工具，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

### 生成证书

以下步骤中的工具命令选项、路径、名称等无限制，用户可依据自身环境和需要替换为其他值。

1. 生成根证书（自签名，包含服务端公钥）：

    根证书用于给数据库服务器证书签名，和发送给客户端用于SSL可信信道验证，本手册生成证书的路径为/home/yashan/YASDB_DATA/config，以该路径为例进行介绍。

    ```shell
    $ openssl req -new -x509 -days 365 -nodes -out root.crt -keyout ca.key -subj "/CN=RootCA"
    ```

2. 生成根证书请求文件和服务器私钥：

    ```shell
    $ openssl req -new -nodes -text \
    -out server.csr \
    -keyout server.key \
    -subj "/CN=server"
    ```

3. 生成服务器证书并签名：

    ```shell
    $ openssl x509 -req -in server.csr -text -days 5 \
    -CA root.crt \
    -CAkey ca.key \
    -CAcreateserial \
    -out server.crt
    ```

4. 生成DH文件：

    ```shell
    $ openssl dhparam -2 -out dhparam.pem -text 2048
    ```

> **Note**: 
>
> 根证书或服务器证书过期后，需重新生成证书。

### 配置参数

1. 在数据库打开SSL可信信道开关，并配置证书路径，路径必须为绝对路径且最长不超过254字节。

    ```sql
    ALTER SYSTEM SET ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_DATA/config/server.crt SCOPE=spfile;
    ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_DATA/config/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_DATA/config/server.key SCOPE=spfile;
    ```

    > **Warn**:
    >
    > 如打开了SSL可信信道开关，但未配置证书路径，或者配置路径不正确，数据库将无法启动。

2. 重启数据库。

## 客户端配置

本手册以Linux平台为例介绍客户端配置。

1. 下载服务端根证书至本地路径，例如/home/yasdb/cert。

2. 在YashanDB客户端文件夹中新建client文件夹，并于client文件夹中新建空文件yasc_env.ini。

    ```shell
    $ mkdir client
    $ cd client
    $ vi yasc_env.ini
    ```

3. 设置环境变量。

    ```shell
    $ vi ~/.bashrc
    export YASDB_HOME=/home/yasdb/yashandb_client
    $ source ~/.bashrc
    ```

    > **Note**: 
    >
    > YASDB_HOME路径需要指向YashanDB客户端文件夹。

4. 在${YASDB_HOME}/client/yasc_env.ini中增加如下配置：

    ```ini
    ssl_root_cer = /home/yasdb/cert/root.crt
    ```

    其中，配置中的路径可指定为绝对或相对路径，但不能为含有../的相对路径，且系统按前255字节长度进行文件读取。
