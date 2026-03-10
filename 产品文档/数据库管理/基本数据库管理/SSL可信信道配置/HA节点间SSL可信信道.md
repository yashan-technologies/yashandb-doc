HA内部节点链路支持SSL可信信道连接，HA节点内部监听支持如下方式：

- 采用客户端监听：配置操作请查阅[数据库服务端SSL可信信道](数据库服务端SSL可信信道)。

- HA节点自身监听：当采用HA自身监听，配置方式详见下文。

## 工具准备

生成证书需使用OpenSSL工具，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

## 生成证书

证书生成步骤同[数据库服务端SSL可信信道](数据库服务端SSL可信信道)。

## 配置参数

1. 在数据库打开HA的SSL可信信道开关，并配置证书路径。其中，路径仅可指定为绝对路径，且最长不超过254字节。

    ```sql
    ALTER SYSTEM SET ha_ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_DATA/config/server.crt SCOPE=spfile;
    ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_DATA/config/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_DATA/config/server.key SCOPE=spfile;
    ```

2. 重启数据库。