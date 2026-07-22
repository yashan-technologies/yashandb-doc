
yex_server沙箱进程是由yasdb进程在特定场景中自发启动的守护进程，YashanDB将相关功能模块独立加载到沙箱进程上，运用进程隔离思想和进程间通信技术，提高相关功能的安全性。

## 使用场景

为提高安全性，使用如下功能时YashanDB会自发启动yex_server沙箱进程独立加载相应的功能模块：

- 调用外置存储过程（外置自定义函数等）。

- 对dblink远端表做INSERT、DELETE、UPDATE以及SELECT操作。

yex_server沙箱进程中开放的配置参数说明如下：

**全局配置参数**

- YDBC_BUFFER_SIZE

    yex_server沙箱进程驱动的内存池大小。默认值为64M，取值必须为正整数或0，有效值域范围为[32M,1T]，若调整该值小于最小值32M不会报错，但实际生效仍采用最小值。

- CHARACTER_SET

    yex_server沙箱进程驱动默认的字符集和实例字符集一致，默认为UTF8。支持配置的字符集有ASCII、ISO88591、GBK、UTF8、GB18030。

**外置存储过程的配置参数**

当前沙箱进程中无需配置外置存储过程的运行参数。

**dblink的配置参数**

请参考[dblink语法说明](dblink语法说明.md)了解dblink涉及的沙箱进程参数。

## 配置步骤

此处以调整沙箱进程内存池参数YDBC_BUFFER_SIZE为例介绍配置步骤。

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

4. 停止yex_server进程。当业务再次应用外置存储过程或dblink后将以新的配置启动yex_server。

    ```shell
    $ ps -efww | grep -w yex_server | grep -v grep  | awk '{print $2}' | xargs -x -i kill -9 {}
    ```
