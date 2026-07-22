YashanDB Python驱动支持Windows和Linux平台，本章将分别介绍YashanDB Python驱动在这两个平台环境下的安装配置过程。

## 步骤1：Python环境准备

验证Python环境和pip，若未安装请下载安装。

首先验证Python和pip是否已安装。安装YashanDB Python驱动需要使用pip，正常情况下pip已内置在Python中无需再次安装。

若尚未安装Python，请从[Python官网](https://www.python.org/downloads/)下载并安装符合Python 3.6及以上版本的Python包，或联系技术人员获取安装包，自行配置Python环境。

::: tabs
== Windows

在Windows系统中，可通过执行python --version命令验证Python环境是否正常，可通过执行pip3 --version命令验证是否安装pip。

```bash
C:\>python --version
3.6.15
C:\>pip3 --version
```

== Linux

在Linux系统中，可通过执行python3 --version命令验证Python环境是否正常，可通过执行pip3 --version命令验证是否安装pip。

```shell
# python3 --version
# Python 3.6.15
# pip3 --version
```
:::

## 步骤2：安装YashanDB C驱动

使用YashanDB Python驱动需要先安装和配置YashanDB C驱动库。YashanDB C驱动安装文件集成在YashanDB客户端安装包中，安装时需获取对应的客户端安装包。

### 步骤2.1：下载C驱动安装包

::: tabs
== Windows

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。
2. 将包名为`YashanDB Connector/C xxx`并标记为Windows的驱动包下载并解压到本地路径，例如`D:\yasdb-driver-c\`。

   安装包解压后在lib文件夹中可得到文件名为`yascli.dll`的文件：


== Linux

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。
2. 将包名为`YashanDB Connector/C xxx`并标记为Linux的驱动包下载并解压到本地路径，例如`/home/yasdb-driver-c/lib`。

   安装包解压后可得到C驱动所需文件：

   * C驱动的头文件：位于include文件夹中。

   * C驱动的库文件：位于lib文件夹中。

:::

### 步骤2.2：设置动态库依赖路径

::: tabs
== Windows

将C驱动的库文件所在文件夹设置到Windows环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入C驱动的库文件所在文件夹，例如`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。

== Linux

1. 编辑bashrc文件：

   ```shell
   $ vi ~/.bashrc
   ```

2. 新起一行增加LD_LIBRARY_PATH搜索路径，指向C驱动的库文件所在文件夹：

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. 保存并退出。

4. 加载bash配置，让修改生效

   ```shell
   $ source ~/.bashrc
   ```
:::

## 步骤3：安装YashanDB Python驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

    > **Note**:
    >
    > 如需选用yaspy驱动包，wheel文件名中的`cp3x-cp3xm`需与本地Python版本匹配（通过`python --version`查看，如Python 3.12对应`cp312-cp312`）。Windows平台推荐选择`yaspy-xx.xx-cp3x-cp3xm-win_amd64.whl`，Linux平台推荐选择`yaspy-xx.xx-cp3x-cp3xm-linux_x86_64.whl`或`yaspy-xx.xx-cp3x-cp3xm-linux_aarch_64.whl`。

2. 将压缩包下载到本地路径，例如/path/YASDB Python。

3. 运行pip3 install *软件包名称* 指令对驱动包进行安装。

    ::: tabs
    == Windows

```bash
C:\>pip3 install yaspy-xx.xx-cp312-cp312-win_amd64.whl
```

    == Linux

> **Note**:
>
> Linux平台若系统全局安装驱动，可能需要切换至root用户。使用虚拟环境时通常不需要。

```shell
# pip3 install yaspy-xx.xx-cp3x-cp3xm-linux_x86_64.whl
```
    :::

安装成功后，Python应用程序可通过引用yaspy模块的方式对YashanDB数据库进行访问操作。
