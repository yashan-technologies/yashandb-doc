## 环境要求

YashanDB ADO.NET驱动要求的.NET版本如下：

- .NET 2.0

- .NET 5.0

- .NET 6.0

## 步骤1：安装.NET环境

1. 通过官方路径下载并安装适用于Liunx的上述版本的.NET。

2. 运行dotnet --version验证.NET环境是否正常：

    ```shell
    $ dotnet --version
    6.0.301
    $ 
    ```

## 步骤2：安装YashanDB C驱动

使用YashanDB ADO.NET驱动需要先安装YashanDB C驱动并设置环境变量，YashanDB C驱动安装文件集成在YashanDB客户端安装包中，安装时需获取对应的客户端安装包。



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


## 安装YashanDB ADO.NET驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取ADO.NET驱动安装包。

2. 将压缩包下载并解压到本地路径，例如/path/Yashandb.Data.YashandbClient。

3. 进入应用程序项目所在文件夹，通过dotnet命令将Yashandb.Data.YashandbClient项目添加到该应用程序项目内。本手册使用的项目文件示例为Examples，具体内容请查阅[YashanDB ADO.NET驱动使用示例](../ADO.NET驱动使用示例)。

   ```bash
   dotnet add reference /path/Yashandb.Data.YashandbClient/Yashandb.Data.YashandbClient.csproj
   ```
    
完成项目引用后，该应用程序即可进行对YashanDB数据库的访问操作。
