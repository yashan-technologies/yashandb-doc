本文以Windows10专业版为例，介绍YashanDB OCI驱动在该环境下的安装配置过程。

> **Note**:
>
> YashanDB OCI驱动依赖C基础开发库，因此应用端还需安装C编译工具，例如gcc、visual studio等。

## 步骤1：下载C驱动安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如`D:\yasdb-driver-c`。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件。

   * include：C驱动的头文件。

   * lib：C驱动的库文件。

## 步骤2：设置环境变量

将`D:\yasdb-driver-c\lib`设置到Windows环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。

## 步骤3：下载OCI安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB OCI驱动安装包。

2. 将OCI驱动安装包下载并解压到本地路径`D:\yasdb-driver-c\lib`。

   zip文件解压后的yas_oci.dll为oci动态库，yas_oci.dll依赖yascli.dll，yascli.dll依赖yas_infra.dll。

   * 如果使用cmake工程，只需依赖yas_oci.dll即可。

   * 如果使用其他链接工具，请按需依赖。

## 步骤4：配置数据源

连接前需确保系统中存在YASDB_HOME系统路径，并已在yasc_service.ini文件中配置了数据源。

1. 进入数据源所处路径%YASDB_HOME%\client\yasc_service.ini。
   
2. 在yasc_service.ini文件中配置数据源，例如`CITEST = 127.0.0.1:1688`。

## 步骤5：测试连接

yasociconntest为测试可执行文件，执行该文件测试OCI连接是否成立。

## 步骤6：（可选）开启日志

日志开启前需确保系统中存在YASDB_HOME系统路径以及YASDB_HOME系统路径下存在client目录。

> **Note**:
>
> 开启日志可能带来OCI性能下降，请谨慎使用。

**日志所处路径**

```c
%YASDB_HOME%\client\yasoci.log
```

**日志开启方式**

方法1：环境上设置系统变量。

```c
YASOCI_LOG = ON
```

方法2：代码中设置进程级系统变量。

```c
(void)putenv("YASOCI_LOG=ON");
```
