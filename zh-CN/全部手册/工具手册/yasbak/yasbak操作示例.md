本示例以XBSA API的方式进行备份恢复对接，文中所列用户、密码、IP、端口、路径等均需替换为实际值。

>**Note**:
>
> 存算一体分布式集群部署中已支持修改节点的IP，即支持在两个分布式集群部署形态完全一致的情况下使用A集群的备份集恢复B集群。如需使用跨分布式集群恢复，需满足如下条件：
>
>- B集群配置文件中的DIN_ADDR和REPLICATION_ADDR的IP必须与LISTEN_ADDR一致，且DIN_ADDR和REPLICATION_ADDR的端口号必须与A集群的配置保持完全一致，否则会导致无法正常恢复集群。
>
>- 执行恢复操作前，需先将分布式集群A的所有备份集均已拷贝至B集群对应的节点备份目录下。

1. 参考[YashanDB服务端安装（命令行）](../../安装和升级/安装部署/YashanDB服务端安装（命令行）/00YashanDB服务端安装（命令行）)文档完成数据库安装部署。

2. 进入安装目录，对数据库的所有节点进行以下检查，判断是否所有节点都开启了归档模式（ARCHIVELOG）。若未开启，请参照[归档管理](../../数据库管理/实例管理/归档管理)开启归档。

    ```shell
    $ yasboot sql -d sys/password@192.168.1.2:1688

    SQL> SELECT database_name,log_mode,open_mode FROM V$DATABASE;
    
    DATABASE_NAME      LOG_MODE          OPEN_MODE  
    ------------------ ----------------- ------------
    yasdb              ARCHIVELOG        READ_WRITE 
    ```

3. 在备份服务器上同步解压YashanDB安装包，该备份服务器需具备XBSA的动态库。

4. 获取yasom服务的连接token，配置位于${YASDB_HOME}/om/\<Cluster_name>/conf/tls.toml中的rpc_secret_key参数值，并对yasbak进行初始化操作。

    ```shell
    $ yasbak deploy -c yashandb -a 192.168.1.3:1675 -k 96ed7a2c90e81a9e -D ./catalog -u sys -p sys_password
    +-------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path            |
    +-------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | db        | 1-1:1  | 26097 | open            | normal          | primary       | 192.168.1.3:1688 | /data/yashan/db-1-1  |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    |          | db        | 1-2:2  | 26220 | open            | normal          | standby       | 192.168.1.3:1690 | /data/yashan/db-1-2  |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    |          | db        | 1-3:3  | 26215 | open            | normal          | standby       | 192.168.1.3:1692 | /data/yashan/db-1-3  |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+----------------+------------------------+
    cluster:yashandb deploy yasbak succeed
    ```

5. 进行备份操作。

    yasrman提供了对接第三方XBSA API的接口，需要在SQL语句中添加PARAMS参数，详情请查阅[yasrman params简介](../yasrman/yasrman使用指导/00yasrman使用指导)。

    ```shell
    YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"

    # 执行全量备份
    $ yasbak run -c yashandb -s "backup database tag 'full_01' parallelism 4 dest client params '${YASRMAN_PARAM}'"
    
    # 执行增量备份
    $ yasbak run -c yashandb -s "backup database incremental level 0 tag 'incr_0'  dest client params '${YASRMAN_PARAM}'"
    $ yasbak run -c yashandb -s "backup database incremental level 1 tag 'incr_1'  dest client params '${YASRMAN_PARAM}'"
    
    # 清理备份
    $ yasbak run -c yashandb -s "delete backupset tag 'full_01'"
    ```
    跟踪各个节点的$YASDB_DATA/log/run/run.log运行日志，可查看该节点备份恢复过程的详细日志。

6. 将数据库重置到nomount。

    恢复前，需要将数据库重置，数据库重置是一项高危操作，需要提供sys用户密码，使用yasbak reset命令对数据库进行重置。

    ```shell
    # 将数据库恢复到nomount状态
    ./yasbak reset -c yashandb -p sys_password
    ```

7. 进行恢复操作。

    ```shell
    YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"
    # 恢复
    $ yasbak run -c yashandb -s "restore database from tag 'full_01' dest client params '${YASRMAN_PARAM}'" --build-all
    ```

8. （可选）对于无需进行yasbak操作的环境，可卸载yasbak。

    ```shell
    # 指定--purge会同时删除catalog目录
    $ yasbak clean -c yashandb --purge

    remove yasbak.env succeed.
    remove yasrman catalog succeed.
    ```
