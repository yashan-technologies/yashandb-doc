yex_server沙箱进程是由yasdb进程在特定场景中自发启动的守护进程，YashanDB将相关功能模块独立加载到沙箱进程上，运用进程隔离思想和进程间通信技术，提高相关功能的安全性。

## 使用场景

为提高安全性，使用如下功能时YashanDB会自发启动yex_server沙箱进程独立加载相应的功能模块：

- 调用外置存储过程（外置自定义函数等）。

- 对dblink远端表做INSERT、DELETE、UPDATE以及SELECT操作。

## 前提条件

需先[安装libaio库和下载Oracle Instant Client安装包](../../数据库管理/基本数据库管理/异构数据库链接配置)。

## 调整内存池大小

yex_server沙箱进程驱动默认的内存池大小为64M，取值范围为\[32M,1T\]，若调整该值小于最小值32M不会报错，但实际生效仍采用最小值，该配置由yex_server.ini配置文件中的YDBC_BUFFER_SIZE参数控制。

YashanDB安装后，yex_server.ini文件不会自动生成，您可以根据实际需求创建文件并调整该参数值。

1. 查询$YASDB_DATA/external/server路径下是否存在yex_server.ini文件，如不存在则创建：

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # 本文以/data/yashan/yasdb_data/db-1-1为例

    $ cd /data/yashan/yasdb_data/db-1-1/external/server
    $ ll
    $ vi yex_server.ini
    ```
    
2. 在yex_server.ini文件中新增或修改如下配置：

    ```shell
    YDBC_BUFFER_SIZE = 64M
    ```

3. 保存并退出编辑。

4. 重启yex_server后配置生效。

由于yex_server会在使用对应功能时自发启动，因此只需要kill当前的yex_server进程即可实现重启yex_server的功能。

    ```shell
    $ ps -efww | grep -w yex_server | grep -v grep  | awk '{print $2}' | xargs -x -i kill -9 {}
    ```

## 调整字符集

yex_server沙箱进程驱动默认的字符集和实例字符集一致，默认为UTF8。支持配置的字符集有ASCII、ISO88591、GBK、UTF8、GB18030。

在yex_server.ini文件中新增或修改如下配置后，重启yex_server生效。

```shell
    CHARACTER_SET=GBK
```