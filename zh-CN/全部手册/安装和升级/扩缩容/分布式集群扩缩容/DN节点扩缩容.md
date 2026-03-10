YashanDB支持通过[yasboot](../../../工具手册/yasboot/00yasboot)工具对分布式集群中的DN节点进行在线扩容，且扩容期间不影响整个集群的使用。

> **Note**: 
>
> - 每个DN节点将由单独的1台服务器承载（后文将称之为“新服务器”），新服务器的操作系统版本建议与现有服务器保持一致。
>
> - 扩容操作增加的是DN上的数据盘而非系统盘，因此新加的服务器在安装前准备中只用挂载数据盘。

以下为扩容操作步骤指引：

1. 准备扩容所需的新服务器，并参照[安装前准备](../../../安装和升级/安装部署/安装前准备/00安装前准备)检查并确保新服务器系统的各项环境已满足要求。

2. 以安装用户登录数据库的现有节点所在服务器。

3. 执行[yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config)命令，生成增加DN节点的配置文件。

   ```shell
   $ yasboot config node gen -c yashandb \
   -u yashan -p password \
   --dn-ip ip1,ip2 \
   --data /dev/nvme0n2
   ```

   执行成功后，会生成[yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件)和[hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件)两个配置文件。

   其中，yashandb_add.toml中的yfs_force_create参数值需调整为true，以保证YFS能强制创建磁盘组。

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

4. 执行如下命令，远程连接新服务器并为其安装YashanDB。

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. 按需在新服务器上完成以下配置。
    
    - 若扩容前数据库已开启[密钥管理](../../../产品安全/加密支持/存储加密/密钥管理)功能且创建了加密对象，新服务器上也必须创建钱包文件的存放路径、将其他服务器上对应的TDE文件夹及其文件复制到该路径下、将钱包的根目录WALLET_ROOT参数配置为该路径

    - 若扩容前其他服务器均已配置开机自启动，新服务器也必须[配置开机自启动](../../安装部署/安装后初始环境/配置开机自启动)。

6. 在上述生成配置文件的服务器上执行如下命令，增加DN节点。

    ``` shell
    # 若扩容前数据库已开启密钥管理，则必须指定--wallet-password Your_keystore_password

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    任务显示成功并不表示扩容任务已全部成功，可能仍有部分后台任务在完成数据的同步等操作。可以通过`task list`命令查看扩容相关的任务是否都成功。

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

8. （可选）备份数据库。

    建议对数据库进行[备份](../../../数据库管理/备份与恢复/00备份与恢复)，确保扩容后有可用于恢复的基线备份集。

9. 将hosts_add.toml中的[[host]]内容复制粘贴到hosts.toml的末尾，以免升级时仍用旧的hosts信息。
