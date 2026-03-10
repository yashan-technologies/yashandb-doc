本文以使用Centos 7.3.1、OCI软件包yashandb-oci-xx.xx-linux-x86_64.tar.gz和YashanDB客户端安装包yashandb-client-xx.xx-linux-x86_64.tar.gz为例，介绍YashanDB OCI驱动在该环境下的安装配置过程。

## 步骤1：下载C驱动安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yasdb-driver-c/。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件（目前包括yasql）。

   * include：C驱动的头文件。

   * lib：C驱动的库文件。

## 步骤2：设置动态库依赖路径

将`/home/yasdb-driver-c/lib`设置到Linux LD_LIBRARY_PATH中，具体操作为：

1. 编辑bashrc文件：

   ```shell
   vi ~/.bashrc
   ```

2. 新起一行增加LD_LIBRARY_PATH搜索路径：

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```
3. 保存并退出。

4. 刷新系统变量配置。

   ```shell
   source ~/.bashrc
   ```

## 步骤3：下载OCI驱动安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB OCI驱动安装包。

2. 将OCI驱动安装包下载并解压到本地路径`/home/yasdb-driver-c/lib`。

   解压后的libyas_oci.so为oci动态库，libyas_oci.so依赖libyascli.so，libyascli.so依赖libyas_infra.so。
   * 如果使用cmake工程，只需依赖libyas_oci.so即可。

   * 如果使用gdb链接，需要依赖libyas_oci.so、libyascli.so和libyas_infra.so。

## 步骤4：配置数据源

连接前需确保系统中存在YASDB_HOME系统路径，并已在yasc_service.ini文件中配置了数据源。

1. 进入数据源所处路径：
   ```shell
   cd $YASDB_HOME/client/
   ```

2. 在yasc_service.ini文件中配置数据源，例如：
   ```shell
   vi yasc_service.ini
   
   CITEST = 192.168.1.10:1688
   ```

## 步骤5：测试连接

yasociconntest为测试可执行文件，执行该文件测试OCI连接是否成立。
```shell
./yasociconntest
```

## 步骤6：（可选）开启日志

日志开启前需确保系统中存在YASDB_HOME系统路径以及YASDB_HOME系统路径下存在client目录。

> **Note**:
>
> 开启日志可能带来OCI性能下降，请谨慎使用。
> Log enabling may lead to OCI performance loss. Use with caution.

**日志所处路径**

```c
$YASDB_HOME/client/yasoci.log
```

**日志开启方式**

方法1：环境上设置系统变量。

```c
export YASOCI_LOG = ON
```

方法2：代码中设置进程级系统变量。

```c
(void)putenv("YASOCI_LOG=ON");
```
