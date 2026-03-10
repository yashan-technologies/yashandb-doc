YashanDB支持通过[yasboot](../../../工具手册/yasboot/00yasboot)工具对存算一体分布式集群部署中的DN组进行扩/缩容，执行具体操作前请仔细阅读相关[注意事项](../扩缩容注意事项)。

> **Note**: 
>
> 如果扩缩容过程中现有节点出现故障、扩缩容失败等异常，请参考[扩缩容异常处理](../扩缩容异常处理)进行处理。

##  新增DN组

1. 如需新增服务器并增加DN组，请先参照[安装前准备](../../安装部署/安装前准备/00安装前准备)检查并确保新服务器系统的各项环境已满足要求。

2. 以安装用户登录数据库的现有节点所在服务器。

3. 结合实际场景，执行相应操作。

    - 场景1：新增服务器并增加DN组

        1. ) 执行[yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config)命令，生成[yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件)和[hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件)配置文件。

        ```shell
        $ yasboot config group gen -c yashandb -u yashan -p password --ip ip1,ip2 --port 22 \
        --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data \
        --log-path /data/yashan/log --node 3 --group 2
        ```

        2. ) 执行如下命令，远程连接新服务器并为其安装YashanDB。

        ```shell
        $ yasboot host add -c yashandb -t hosts_add.toml
        ```

        >  **Note**: 
        >
        > 按需在**新服务器**上完成以下配置：
        >
        > - 若扩容前数据库已开启[资源管理](../../../数据库管理/资源管理/00资源管理)功能，在新服务器上执行[yasboot host cgroup命令](../../../工具手册/yasboot/yasboot命令介绍/yasboot host)创建cgroup目录。
        > 
        >```shell
        >$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        >```
        > 
        > - 若扩容前其他服务器均已配置开机自启动，新服务器上也必须[配置开机自启动](../../安装部署/安装后初始环境/配置开机自启动)。
    - 场景2：在现有服务器上增加DN组

        执行[yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config)命令，生成[yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件)配置文件。

        ```shell
        $ yasboot config group gen -c yashandb --host-ids host0001,host0002 --node 3 --group 2
        ```

    >  **Note**: 
    >
    > 对于3节点及以上的DN组，HA_ELECTION_ENABLED参数是强制开启的，若需要关闭，可在扩容成功后修改该参数。

4. 在上述生成配置文件的服务器上执行如下命令，增加DN组。

    ``` shell
    $ yasboot group add -c yashandb -t yashandb_add.toml
    ```

    任务显示成功并不表示扩容任务已全部成功，可能仍有部分后台任务在完成数据的同步等操作。可以通过`task list`命令查看扩容相关的任务是否都成功。

5. （可选）备份数据库。

    建议对数据库进行[备份](../../../数据库管理/备份与恢复/00备份与恢复)，确保扩容后有可用于恢复的基线备份集。

6. 若采用新增服务器的方式进行扩容，扩容完成后需要把hosts_add.toml中的[[host]]内容复制粘贴到hosts.toml的末尾，以免升级时仍用旧的hosts信息。

7. 可按需开启DN组内节点的自动选主功能，保障业务连续性：

    - 一主一备环境：需手动可以开启[yasom仲裁选主](../../../高可用/自动选主配置/一主一备yasom仲裁选主)。

    - 一主多备环境：yasboot会自动开启[自动选主](../../../高可用/自动选主配置/一主多备自动选主)。

## 删除DN组

1. 以安装用户登录数据库安装服务器。


2. 执行如下命令，查看待删除的目标组ID。

    ```shell
    $ yasboot cluster status -c yashandb -d
    # nodeid中横线前的部分即为组ID，例如1-1:1对应的组ID为1
    ```

3. 执行如下命令，删除DN组。

    ```shell
    # 可以增加--with-host参数，成功删除节点组后继续删除当前分布式集群中所有无数据库节点、无yasom进程的空服务器的信息
    $ yasboot group remove --group-ids 3,4 --purge --force -c yashandb
    ```

4. （可选）备份数据库。
    
    建议对数据库进行[备份](../../../数据库管理/备份与恢复/00备份与恢复)，确保缩容后有可用于恢复的基线备份集。

5. 若缩容时指定了--with-host参数（即移除了空服务器），缩容完成后需要更新hosts.toml文件的[[host]]内容，删除对应服务器相关信息，以免升级时仍用旧的hosts信息。
