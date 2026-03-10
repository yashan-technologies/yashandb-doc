## 环境要求

- 操作系统版本：Windows 10及以上。

- .NET版本：YashanDB ADO.NET驱动要求的.NET版本如下：
    - .NET 2.0

    - .NET 5.0

    - .NET 6.0

## 安装64位YashanDB ADO.NET驱动



安装64位YashanDB ADO.NET驱动时，下述步骤中所需的安装包均要求使用64位的软件包。

### 步骤1：安装64位.NET环境

1. 通过官方路径下载并安装满足版本要求的.NET。

2. 安装Visual Studio或类似的集成开发环境来编写和编译C#源代码，本手册以Visual Studio为例进行介绍。

3. 运行dotnet --version验证.NET环境是否正常：

    ```shell
    C:\>dotnet --version
    6.0.301
    ```

### 步骤2：安装64位YashanDB C驱动

使用YashanDB ADO.NET驱动需要先安装YashanDB C驱动并设置环境变量，YashanDB C驱动安装文件集成在YashanDB客户端安装包中，安装时需获取对应的客户端安装包。



#### 步骤1：下载C驱动安装包

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如D:\yasdb-driver-c\。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件。

   * include：C驱动的头文件。
   
   * lib：C驱动的库文件。

#### 步骤2：设置环境变量

将C驱动的库文件所在文件夹设置到Windows环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入C驱动的库文件所在文件夹，例如`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。






### 步骤3：安装64位YashanDB ADO.NET驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

2. 将压缩包下载并解压到本地路径，例如/path/Yashandb.Data.YashandbClient。

3. 编辑应用程序项目，将Yashandb.Data.YashandbClient项目引用到该项目。本手册使用的项目文件示例为Examples，具体内容请查阅[ADO.NET驱动使用示例](../ADO.NET驱动使用示例)。

    1. ) 打开应用程序项目Examples，单击【文件 > 添加 > 现有项目】，单击Yashandb.Data.YashandbClient所在路径，选择Yashandb.Data.YashandbClient.csproj文件，将Yashandb.Data.YashandbClient加入到应用所在解决方案内。
    
	2. ) 右键单击Examples应用程序项目，依次选择【添加 > 项目引用 > 项目】，勾选Yashandb.Data.YashandbClient选项。
    

完成项目引用后，该应用程序即可进行对YashanDB数据库的访问操作。

## 安装32位YashanDB ADO.NET驱动

>**Note**:
>
> **不推荐**在64位的操作系统中安装32位的驱动程序。



安装32位YashanDB ADO.NET驱动时，下述步骤中所需的安装包均要求使用32位的软件包。

### 步骤1：安装32位.NET环境

1. 通过官方路径下载并安装满足版本要求的.NET。

2. 安装Visual Studio或类似的集成开发环境来编写和编译C#源代码，本手册以Visual Studio为例进行介绍。

3. 运行dotnet --version验证.NET环境是否正常：

    ```shell
    C:\>dotnet --version
    6.0.301
    ```

### 步骤2：安装32位YashanDB C驱动

使用YashanDB ADO.NET驱动需要先安装YashanDB C驱动并设置环境变量，YashanDB C驱动安装文件集成在YashanDB客户端安装包中，安装时需获取对应的客户端安装包。



#### 步骤1：下载C驱动安装包

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如D:\yasdb-driver-c\。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件。

   * include：C驱动的头文件。
   
   * lib：C驱动的库文件。

#### 步骤2：设置环境变量

将C驱动的库文件所在文件夹设置到Windows环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入C驱动的库文件所在文件夹，例如`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。







### 步骤3：检查依赖项

仅在安装32位驱动时，需要执行本操作。

1. 检查`C:\Windows\SysWOW64`文件夹中是否包含**32位**的ucrtbased.dll和vcruntime140d.dll。
      
   - 若无，需先自行获取ucrtbased.dll和vcruntime140d.dll文件并保存至`C:\Windows\SysWOW64`文件夹中，例如可以通过安装Visual Studio等方式获取。

   - 若有，则可直接执行后续操作。





### 步骤4：安装32位YashanDB ADO.NET驱动

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

2. 将压缩包下载并解压到本地路径，例如/path/Yashandb.Data.YashandbClient。

3. 编辑应用程序项目，将Yashandb.Data.YashandbClient项目引用到该项目。本手册使用的项目文件示例为Examples，具体内容请查阅[ADO.NET驱动使用示例](../ADO.NET驱动使用示例)。

    1. ) 打开应用程序项目Examples，单击【文件 > 添加 > 现有项目】，单击Yashandb.Data.YashandbClient所在路径，选择Yashandb.Data.YashandbClient.csproj文件，将Yashandb.Data.YashandbClient加入到应用所在解决方案内。
    
	2. ) 右键单击Examples应用程序项目，依次选择【添加 > 项目引用 > 项目】，勾选Yashandb.Data.YashandbClient选项。
    


4. 若在64位系统中混用32位驱动，还需将项目及其子项目（例如Yashandb.Data.YashandbClient）设置为强制32位编译。
    
    1. ) 右键单击应用程序项目，选择【属性】。

    2. ) 打开【生成】选项卡，选择【目标平台】为【x86】，单击【保存】。
