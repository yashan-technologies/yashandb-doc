YashanDB supports online expansion of DN nodes in Distributed Cluster Deployment through the [yasboot](../../../Tools Guide/yasboot/00yasboot) tool, and the expansion does not affect the usage of the entire cluster.

> **Note**: 
>
> - Each DN node will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).
>
> - The scaling operation will add DN data disks instead of system disks, so you only need to mount data disks on the new server in the preparation for installation.

The following is a guide to the operation steps:

1. Prepare the new servers required for expansion, and refer to [Pre-installation Preparation](../../../安装和升级/安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command to generate the configuration file for adding DN node(s).

   ```shell
   $ yasboot config node gen -c yashandb \
   -u yashan -p password \
   --dn-ip ip1,ip2 \
   --data /dev/nvme0n2
   ```

    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件). 

   The parameter `yfs_force_create` is needed to be set to `true`  for ensuring that YFS can create disk groups compulsorily.

   ```shell
   $ vi yashandb_add.toml
   ...
   [[group.diskgroup]]
       au_size = "1M"
       disk_size = ""
       name = "DG0"
       redundancy = "EXTERNAL"
       yfs_force_create = true
   ...
   ```

4. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. Complete the following configurations on the new server(s) as required.
    
    - If the database has enabled the [key management](../../../产品安全/加密支持/存储加密/密钥管理) function and created encrypted objects before expansion, the new server must also create the wallet file storage path, copy the corresponding TDE folder and its files from other servers to this path, and configure the wallet root directory WALLET_ROOT parameter to this path.

    - If all other servers have been configured for auto-start before the expansion, the new server must also be [configured for auto-start](../../安装部署/安装后初始环境/配置开机自启动).

6. On the server where the configuration file was generated, execute the following command to add DN node(s).

    ``` shell
    # If the database has enabled key management before expansion, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

8. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../数据库管理/备份恢复/00备份恢复) of the database to ensure there is a baseline backup set available for recovery after scaling.

9. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.