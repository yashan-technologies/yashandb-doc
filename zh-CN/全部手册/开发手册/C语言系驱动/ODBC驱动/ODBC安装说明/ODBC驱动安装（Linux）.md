本文以Centos 7.3.1为例，介绍YashanDB ODBC驱动在该环境下的安装配置过程。

## 步骤1：安装unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## 步骤2：安装YashanDB C驱动



### 步骤1：下载C驱动安装包

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yasdb-driver-c/。

   安装包解压后可得到C驱动所需文件：

   * bin：C驱动的可执行文件（目前包括yasql）。

   * C驱动的头文件：位于include文件夹中。

   * C驱动的库文件：位于lib文件夹中。



## 步骤3：安装YashanDB ODBC驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

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
URL          = 127.0.0.1:1688
USER         = sys
PWD          = sys
```

配置项中的URL为数据源的完整URL，请参考[C驱动yacConnect函数](../../C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect)中的URL参数，配置URL参数后，SERVER和PORT参数将不会再生效。

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
