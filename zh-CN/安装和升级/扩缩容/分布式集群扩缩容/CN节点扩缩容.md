YashanDB支持通过[yasboot](../../../工具手册/yasboot/00yasboot)工具对分布式集群中的CN节点进行在线扩容，且扩容期间不影响整个集群的使用。

> **Note**:
>
> 每个CN节点将由单独的1台服务器承载（后文将称之为“新服务器”），新服务器的操作系统版本建议与现有服务器保持一致。

以下为扩容操作步骤指引：

1. 准备扩容所需的新服务器，并参照[安装前准备](../../../安装和升级/安装部署/安装前准备/00安装前准备.md)检查并确保新服务器系统的各项环境已满足要求。

2. 以安装用户登录数据库的现有节点所在服务器。

3. 执行[yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config.md)命令，生成增加CN节点的配置文件。

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p password \
    --cn-ip ip1,ip2 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log
    ```
    执行成功后，会生成[yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件.md)和[hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件.md)两个配置文件。
    
4. 执行如下命令，远程连接新服务器并为其安装YashanDB。

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. 按需在新服务器上完成以下配置。
    
    - 若扩容前数据库已开启[资源管理](../../../数据库管理/资源管理/00资源管理)功能，新服务器上也需执行[yasboot host cgroup命令](../../../工具手册/yasboot/yasboot命令介绍/yasboot host)创建cgroup目录。

        ```shell
        $ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        ```

    - 若扩容前其他服务器均已配置开机自启动，新服务器也必须[配置开机自启动](../../安装部署/安装后初始环境/配置开机自启动.md)。
    

6. 在上述生成配置文件的服务器上执行如下命令，增加CN节点。

    ``` shell
    # 若扩容前数据库已开启密钥管理，则必须指定--wallet-password Your_keystore_password

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    任务显示成功并不表示扩容任务已全部成功，可能仍有部分后台任务在完成数据的同步等操作。可以通过`task list`命令查看扩容相关的任务是否都成功。

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

7. （可选）备份数据库。

    建议对数据库进行[备份](../../../数据库管理/备份与恢复/00备份与恢复)，确保扩容后有可用于恢复的基线备份集。

9. 将hosts_add.toml中的[[host]]内容复制粘贴到hosts.toml的末尾，以免升级时仍用旧的hosts信息。
