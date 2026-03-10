本文以Centos 7.3.1为例，介绍YashanDB ODBC驱动在该环境下的安装配置过程。

## 步骤1：安装unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## 步骤2：安装YashanDB C驱动



### 下载C驱动安装包


1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yasdb-driver-c/。

   安装包解压后可得到C驱动所需文件：

   * C驱动的头文件：位于include文件夹中。

   * C驱动的库文件：位于lib文件夹中。



### 设置动态库依赖路径

1. 编辑bashrc文件：

   ```shell
   $ vi ~/.bashrc
   ```

2. 新起一行增加LD_LIBRARY_PATH搜索路径，指向C驱动的库文件所在文件夹：

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. 保存并退出。

4. 刷新系统变量配置。

   ```shell
   $ source ~/.bashrc
   ```


## 步骤3：安装YashanDB ODBC驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。

2. 将ODBC驱动安装包下载并解压到本地路径，例如/home/yashandb_odbc/。

3. 安装ODBC驱动（so路径仅为示例，请使用实际的libyas_odbc.so路径）。

   ```shell
   $ vi /etc/odbcinst.ini
   
   [YashanDB]
   Description     = ODBC for YashanDB
   Driver          = /home/yashandb_odbc/libyas_odbc.so
   Setup           = /home/yashandb_odbc/libyas_odbc.so
   Driver64        = /home/yashandb_odbc/libyas_odbc.so
   Setup64         = /home/yashandb_odbc/libyas_odbc.so
   FileUsage       = 1
   ```

## 步骤4：配置数据源

```shell
$ vi /etc/odbc.ini

[YASTEST]
Description  = YashanTest
Driver       = YashanDB
SERVER       = 127.0.0.1
PORT         = 1688
URL          = 127.0.0.1:1688[/pdb_name]
USER         = sys
PWD          = sys
```

配置项中的URL为数据源的完整URL，配置URL参数后，SERVER和PORT参数将不会再生效。URL格式如下：

* 单IP： `host:port[/pdb_name]`。

* 多IP：`serverType:host:port,host:port,host:port,host:port[/pdb_name]`，多个地址间采用`,`分隔，连接时根据serverType配置对相应节点进行轮询连接。

* 多IP组：`serverType:host:port,host:port;host:port,host:port[/pdb_name]`，多个IP组间采用`;`分隔，连接时先在组内根据serverType配置对相应节点进行轮询连接，当组内所有连接均失败后按顺序优先级（越靠前优先级越高）访问下一组。

参数含义：

* host:port[/pdb_name]：服务端的监听地址，如需连接部署为容器数据库的YashanDB时，必须使用v23.5.1.100及以上版本的ODBC驱动并指定正确的[目标容器名称](../../../../数据库管理/租户管理/管理PDB/查看PDB信息)，省略则默认连接至根容器。

*  serverType：连接模式，可选项包括[primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance]。

此处可进行字符集设置（前提是使用ANSI驱动，linux默认ANSI ODBC字符集是UTF8），可使用以下数据源配置设置字符集：

```shell
# 目前支持的字符集有：ASCII、ISO88591、GBK、UTF8、UTF16、GB18030
CHARACTER_SET = UTF8
```

## 步骤5：测试连接

```shell
# 数据源名称以yastest为例
$ isql yastest -v

# 查看数据源名称
$ odbcinst -q -s

# 查看驱动名称
$ odbcinst -q -d

# 查看驱动配置
$ odbcinst -j
```

## 步骤6：（可选）使用yasodbc自带test程序测试连接

执行yasodbctest，数据源名称以yastest为例。

```shell
$ ./yasodbctest yastest sys sys
# 或者
$ ./yasodbctest yastest
```

使用exit退出。

```shell
> exit
```

![](./image/yasodbctest.png)
