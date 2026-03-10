分布式节点间通信网络（DIN）支持SSL可信信道。

分布式节点进行跨节点通信时既可能是主动连接的一方（视作SSL握手的客户端），也可能是被动连接的一方（视作SSL握手的服务端），因此每个节点都需要配置服务器私钥和服务器证书等相关参数。

> **Caution**：
>
> - 分布式节点间内部通信网络是否开启SSL可信信道必须整个集群统一，不可部分节点配置或配置不一致。
>
> - 所有分布式节点的服务器证书应使用同一份根证书签名，否则将报错。
>
> - 如打开了SSL可信信道开关，但未配置证书路径，或者配置路径不正确，数据库将无法启动。

## 工具准备

生成证书需使用OpenSSL工具，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

## 生成证书

以下步骤中的工具命令选项、路径、名称等无限制，用户可依据自身环境和需要替换为其他值。

为简化操作，本手册将以服务器为单位管理服务器私钥和证书，即同一服务器上运行的所有分布式节点共用同一份服务器证书，保存在$YASDB_HOME/ssl目录下。同时，根证书、服务器私钥、服务器证书和DH文件等均只生成一份，整个集群节点间进行拷贝。

1. 生成根证书（自签名，包含服务端公钥）：

   根证书用于给分布式节点进行证书签名，整个分布式集群应使用同一份根证书。

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

5. 拷贝证书到其他服务器的$YASDB_HOME/ssl目录下。

   实际操作时，用户可权衡自身对证书管理便捷性和安全性的需要，对配置流程和方式进行调整。

   如需按节点为单位管理证书，可将证书保存在$YASDB_DATA/config/ssl目录下并按节点进行证书拷贝，每个分布式节点使用独立的证书。

   如需提高安全性，可在配置另一证书管理单位（服务器或节点）时仅拷贝根证书并执行流程2 - 4，单独生成服务器私钥和证书。

> **Note**：
>
> 根证书或服务器证书过期后，需重新生成证书。

## 配置参数

1. 在数据库打开SSL可信信道开关，并配置证书路径。其中，路径仅可指定为绝对路径，且最长不超过254字节。用户可依据自身环境和需要替换示例中的证书路径。

   ```sql
   ALTER SYSTEM SET din_ssl_enable = ON SCOPE=spfile;
   ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_HOME/ssl/server.crt SCOPE=spfile;
   ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_HOME/ssl/dhparam.pem SCOPE=spfile;
   ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_HOME/ssl/server.key SCOPE=spfile;
   ```

2. 确认所有分布式节点都完成证书制作和参数配置且无误后，重启数据库。
