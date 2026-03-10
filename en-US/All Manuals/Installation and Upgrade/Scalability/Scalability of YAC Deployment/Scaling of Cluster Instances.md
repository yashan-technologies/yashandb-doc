YashanDB supports online expansion of instance nodes in YAC Deployment (including YCS instances and database instances) through the [yasboot](../../../Tools Guide/yasboot/00yasboot) tool, and the expansion does not affect the usage of the entire cluster.

> **Note**: 
>
> - Currently, it is not possible to reduce instances within the cluster.
>
> - Each instance will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).
>
> - If the expansion fails, please refer to [Scaling Issues Handling](../Scaling Issues Handling) for resolution.

1. Prepare the new servers required for expansion, and refer to [Pre-installation Preparation](../../../安装和升级/安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing instance of the database is located as the installation user. If it is a primary - standby cluster, log in to the server where any instance of the primary cluster is located.

3. Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command to generate the configuration file for adding standby instance(s).

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 2 \
    --vips vip1,vip2        # If the original cluster has not configured VIP, the --vips option does not need to be specified
    ```
    Upon successful execution, two configuration files will be generated: [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件).

4. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. Complete the following configurations on the new server(s) as required.
    
    - If the database has enabled the [key management](../../../产品安全/加密支持/存储加密/密钥管理) function and created encrypted objects before expansion, the new server must also create the wallet file storage path, copy the corresponding TDE folder and its files from other servers to this path, and configure the wallet root directory WALLET_ROOT parameter to this path.

    - If all other servers have been configured for auto-start before the expansion, the new server must also be [configured for auto-start](../../安装部署/安装后初始环境/配置开机自启动).

6. On the server where the configuration file was generated, execute the following command to add instance(s).

    ``` shell
    # If the database has enabled key management before expansion, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

7. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../数据库管理/备份恢复/00备份恢复) of the database to ensure there is a baseline backup set available for recovery after scaling.

8. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.