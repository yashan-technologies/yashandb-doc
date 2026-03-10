yasboot系列工具提供TLS加密功能，将yasboot、yasom、yasagent间传输的信息以X.509的格式进行加密，提升产品安全能力。

安装部署YashanDB后，可按需开启该功能。开启前需准备xxx.key私钥文件和xxx.crt公钥证书文件，其中公钥证书文件可由CA颁发，也可通过自签名生成。

## 自签名生成私钥文件和证书文件

若已有公钥，可跳过本操作。

1. 在任意具有读写权限的目录中，新建`openssl.conf`文件并输入以下内容。

``` conf 
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
prompt = no

[req_distinguished_name]
CN = yashan   # yashan为server_name，可根据实际需求命名

[v3_req]
subjectAltName = @alt_names

[alt_names]
DNS.1 = yashan # yashan为server_name，需与req_distinguished_name中的配置保持一致
```

2. 执行如下命令，生成`rpc.key`私钥文件和`rpc.crt`公钥证书文件。

```shell
$ openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout rpc.key -out rpc.crt -config openssl.conf -extensions v3_req
```

参数介绍：

- -days: 证书有效天数。
- -keyout: 私钥文件名称。
- -rpc.crt: 公钥证书文件名称。
- -config: 基于此文件生成私钥和公钥。

## 开启TLS加密

### 配置`tls.toml`文件

1. 将`rpc.key`私钥文件和`rpc.crt`公钥证书文件上传（或复制）至每台部署了YashanDB的服务器。

2. 修改**安装目录**和所有**部署目录**下的`/om/集群名称/conf/tls.toml`文件。

```toml
rpc_tls_enable = false              # 修改为true
rpc_server_name = ""                # 修改为生成证书使用的server_name
rpc_tls_cert_file = ""              # 修改为rpc.crt在该服务器上的绝对路径
rpc_tls_key_file = ""               # 修改为rpc.key在该服务器上的绝对路径
```

### 重启yasom和yasagent
1. 在每一台yasom所在服务器执行如下命令，重启yasom：

``` shell
$ yasboot process yasom restart -c yashandb 
```

2. 在所有服务器上执行如下命令，重启yasagent：

``` shell
$ yasboot process yasagent restart -c yashandb 
```

### 验证是否开启成功

执行如下命令，打印节点的信息并根据返回结果确认是否成功开启TLS加密。

- 场景1：执行命令不报错且成功打印数据库各节点，表示成功开启TLS加密。
- 场景2：执行命令不报错但部分数据库节点信息未正常打印，需排查该节点运行状态是否正常，再检查该节点所在服务器是否正确开启TLS加密。
- 场景3：执行命令报x.509等错误，可能在证书配置上存在错误，请检查所有修改过的`tls.toml`文件中，`rpc.key`私钥文件和`rpc.crt`公钥证书文件的路径是否正确。

```shell
$ yasboot cluster status -c yashandb -d
+--------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                        |
+--------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
```

## 关闭TLS加密

### 修改`tls.toml`文件

修改**安装目录**和所有**部署目录**下的`/om/集群名称/conf/tls.toml`文件，把`rpc_tls_enable`设为false即可。

```toml
rpc_tls_enable = true              # 修改为false
```

### 重启yasom和yasagent
1. 在安装了yasom的服务器上重启yasom：

``` shell
$ yasboot process yasom restart -c yashandb 
```

2. 在所有服务器上重启yasagent：

``` shell
$ yasboot process yasagent restart -c yashandb 
```

### 验证是否关闭成功

查看此命令是否能在数据库正常的下，打印出数据库各个节点的信息。

执行如下命令，打印节点的信息并根据返回结果确认是否成功关闭TLS加密。

- 场景1：执行命令不报错且成功打印数据库各节点，表示成功关闭TLS加密。
- 场景2：执行命令不报错但部分数据库节点信息未正常打印，需排查该节点运行状态是否正常，再检查该节点所在服务器是否正确关闭TLS加密。
- 场景3：执行命令报x.509等错误，可能在证书配置上存在错误，请检查所有修改过的`tls.toml`文件中，`rpc.key`私钥文件和`rpc.crt`公钥证书文件的路径是否正确。

```shell
$ yasboot cluster status -c yashandb -d
+------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                            |
+------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
```
