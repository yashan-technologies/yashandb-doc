> **Caution**: 
> 
> 通过Docker容器与特定镜像包可快速轻量化部署yashan模式的单机单节点YashanDB。

## 准备工作

1. 以安装用户登录Docker容器宿主机。

2. 创建2个目录用于映射YashanDB的数据目录和yasboot工具目录：

    ```shell
    $ cd /home/yashan

    $ mkdir yasboot
    $ mkdir data
    ```
    
    数据库的相关文件会相应保存至上述目录，请确保磁盘容量充足。

## 步骤1：导入安装包

执行如下命令，导入安装包：

```shell
$ docker load -i yashandb-image-{version_number}-linux-x86_64.tar.gz
```

导入完成后，可以使用docker images命令查看导入的镜像，回显信息如下：

```shell
$ docker images
REPOSITORY                                 TAG                                               IMAGE ID       CREATED        SIZE
yashandb                                   yashandb-image-{version_number}-linux-x86_64   2d27c4f55115   29 hours ago   912MB
```

## 步骤2：启动容器

执行如下命令，启动容器：

```shell
$ docker run -d -p 1688:1688 -v /home/yashan/data:/data/yashan -v /home/yashan/yasboot:/home/yashan/.yasboot -e SYS_PASSWD=password --name yashandb yashandb:yashandb-image-{version_number}-linux-x86_64
```

容器运行相关参数说明：

|  参数名| 参数描述|
| ------ | ------------------------------------------ |
| -d     | 后台运行容器                               |
| -p     | 指定容器端口映射，容器内暴露的端口号为1688 |
| -v     | 指定容器文件路径映射，将在宿主机中准备的YashanDB的数据目录和yasboot工具目录挂载到容器内，多个目录则指定多次   |
| --name | 指定容器名称                               |
| -e     | 指定环境变量                               |

支持的环境变量如下：

|  参数名| 参数描述|
| -------------- | ------------------------------------------------------------ |
| SYS_PASSWD     | sys用户的密码（必须配置）                                |
| DB_BLOCK_SIZE  | 数据块大小                                                   |
| YAS_{建库参数} | 支持所有[建库参数](../全部手册/工具手册/yasboot/建库参数)，格式为`YAS_参数名`（例如参数REDO_FILE_SIZE需写成YAS_REDO_FILE_SIZE），此处配置过的参数建库后不可修改 |

## 步骤3：检查安装结果

执行如下命令，检查安装结果：

```shell
$ docker logs yashandb
init
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
+------------------------------------------------------------------------------------------------------+
| hostid   | group | node_type | node_name | listen_addr  | replication_addr | data_path               |
+------------------------------------------------------------------------------------------------------+
| host0001 | dbg1  | db        | 1-1       | 0.0.0.0:1688 | 127.0.0.1:1689   | /data/yashan/yasdb_data |
+----------+-------+-----------+-----------+--------------+------------------+-------------------------+

Generate config completed
    REDO_FILE_SIZE = "128M"
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
checking install profile.toml...
install version: yashandb 23.2.4.24
update host to yasom...
+------------------------------------------------------------------------------------------------------------+
| type | uuid             | name               | hostid | index    | status  | return_code | progress | cost |
+------------------------------------------------------------------------------------------------------------+
| task | cebb2746ef35401b | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 33   |
+------+------------------+--------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

DeployYasdbCluster任务状态为SUCCESS时，表示数据库安装成功。

## 步骤4：连接数据库

1. 执行如下命令，进入容器：

    ```shell
    $ docker exec -it yashandb /bin/bash -l
    ```

2. 使用[yasql](../全部手册/工具手册/yasql/00yasql)工具连接数据库，查看实例状态。

    ```shell
    $ yasql sys/********@0.0.0.0:1688
    YashanDB SQL Enterprise Edition Release {version_number} aarch64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} aarch64 - Linux

    SQL> SELECT STATUS FROM V$INSTANCE;

    STATUS        
    ------------- 
    OPEN         


    SQL> SELECT database_name FROM v$database;

    DATABASE_NAME                                                    
    ---------------------------------------------------------------- 
    yashandb                                                        


    ```

3. （可选）创建数据库用户并赋权，更多操作请查阅[用户管理](../全部手册/产品安全/身份标识与鉴别/管理用户)。

   ```shell
   SQL> CREATE USER sales IDENTIFIED BY sales;
      
   SQL> GRANT CONNECT TO SALES;
   ```

## 附录：基于docker管理数据库

### 进入容器

```shell
$ docker exec -it yashandb /bin/bash -l
```

进入容器后，可使用[yasql](../全部手册/工具手册/yasql/00yasql)、[yasboot](../全部手册/工具手册/yasboot/00yasboot)等工具连接或管理数据库。

### 通过容器启停数据库

可以直接通过docker命令启停数据库集群，其效果等同于进入容器后再执行yasboot工具的启停相关命令。

- 停止数据库

  ```shell
  $ docker stop yashandb
  ```

- 启动数据库

  ```shell
  $ docker start yashandb
  ```

- 重启数据库

  ```shell
  $ docker restart yashandb
  ```
