YashanDB启用[TLCP可信信道](../../产品安全/加密支持/可信信道.html#tlcp)连接要求由Gmssl工具生成相关证书，在完成相关证书的正确配置后，通讯时进行客户端到服务端的安全验证。

> **Caution**: 
>
> - 一旦服务器开启TLCP可信信道，所有的客户端都必须正确配置证书才能连接到数据库。
>
> - 启用TLCP可信信道和用户密码认证无关联，用户登录数据库仍需输入密码。

## 服务端配置

### 工具准备

生成证书需使用GmSSL工具，请先参照[依赖项准备](../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

### 生成证书

以下步骤中的工具命令选项、路径、名称等无限制，用户可依据自身环境和需要替换为其他值。

1. 生成服务端证书：

    - 生成根证书

        ```shell
        $ gmssl sm2keygen -pass 1234 -out rootcakey.pem
        $ gmssl certgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN ROOTCA -days 3650 -key rootcakey.pem -pass 1234 -out rootcacert.pem -key_usage keyCertSign -key_usage cRLSign -ca
        $ gmssl certparse -in rootcacert.pem
        ```

    - 生成CA证书

        ```shell
        $ gmssl sm2keygen -pass 1234 -out cakey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN "Sub CA" -key cakey.pem -pass 1234 -out careq.pem
        $ gmssl reqsign -in careq.pem -days 365 -key_usage keyCertSign -path_len_constraint 0 -cacert rootcacert.pem -key rootcakey.pem -pass 1234 -out cacert.pem -ca
        $ gmssl certparse -in cacert.pem
        ```

    - 使用CA证书签发签名证书

        ```shell
        $ gmssl sm2keygen -pass 1234 -out signkey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost -key signkey.pem -pass 1234 -out signreq.pem
        $ gmssl reqsign -in signreq.pem -days 365 -key_usage digitalSignature -cacert cacert.pem -key cakey.pem -pass 1234 -out signcert.pem
        $ gmssl certparse -in signcert.pem
        ```

    - 使用CA证书签发加密证书

        ```shell
        $ gmssl sm2keygen -pass 1234 -out enckey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost -key enckey.pem -pass 1234 -out encreq.pem
        $ gmssl reqsign -in encreq.pem -days 365 -key_usage keyEncipherment -cacert cacert.pem -key cakey.pem -pass 1234 -out enccert.pem
        $ gmssl certparse -in enccert.pem

        $ cat signcert.pem > double_certs.pem
        $ cat enccert.pem >> double_certs.pem
        $ cat cacert.pem >> double_certs.pem
        ```

2. 生成客户端证书。

    ```shell
    $ gmssl sm2keygen -pass 1234 -out clientkey.pem
    $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN Client -key clientkey.pem -pass 1234 -out clientreq.pem
    $ gmssl reqsign -in clientreq.pem -days 365 -key_usage digitalSignature -cacert cacert.pem -key cakey.pem -pass 1234 -out clientcert.pem
    $ gmssl certparse -in clientcert.pem
    ```

3. 配置tlcpPass文件。

    ```shell
    TLCP_ENC_PASS = 1234
    TLCP_SIGN_PASS = 1234
    ```

### 配置参数

1. 在数据库打开TLCP可信信道开关，并配置证书路径，路径必须为绝对路径且最长不超过254字节。

    ```sql
    ALTER SYSTEM SET ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ENCRYPT_TYPE = tlcp SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CERT_FILE = /data/tlcp/double_certs.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_SIGNKEY_FILE = /data/tlcp/signkey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_ENCKEY_FILE = /data/tlcp/enckey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CACERT_FILE = /data/tlcp/cacert.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_PASS_FILE = /data/tlcp/tlcpPass SCOPE=spfile;
    ```

    > **Warn**:
    >
    > 如果打开了TLCP可信信道，但未配置证书路径或配置路径不正确，数据库将无法启动。

2. 重启数据库。

## 客户端配置

本文以Linux平台为例介绍客户端配置。

1. 下载服务端根证书至本地路径，例如/data/tlcp。

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
    TLCP_CACERT_FILE=/data/tlcp/rootcacert.pem
    TLCP_CERT_FILE=/data/tlcp/clientcert.pem
    TLCP_CLIENT_KEY_FILE=/data/tlcp/clientkey.pem
    TLCP_CLIENT_KEY_PASS = 1234
    ```

    其中，配置中的路径可指定为绝对或相对路径，但不能为含有../的相对路径，且系统按前255字节长度进行文件读取。
