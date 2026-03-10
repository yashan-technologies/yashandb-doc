本文以Windows10专业版为例，介绍YashanDB ODBC驱动在该环境下的安装配置过程。

> **Note**:
> 
>YashanDB ODBC驱动依赖C基础开发库，因此应用端还需安装C编译工具，例如gcc、visual studio等。

## 步骤1：下载C驱动安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如`D:\yasdb-driver-c`。

   安装包解压后可得到以下文件夹：

   - bin：C驱动的可执行文件，如果是32位系统对应的安装包则无bin文件夹。

   - include：C驱动的头文件。
   
   - lib：C驱动的库文件。

## 步骤2：设置环境变量

将`D:\yasdb-driver-c\lib`设置到Windows 环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。

## 步骤3：下载ODBC驱动安装包

1. 依据安装手册[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB ODBC驱动安装包。

2. 将ODBC驱动安装包下载并解压到本地路径，例如`D:\yasdb-driver-odbc`。

3. 确认install.bat、uninstall.bat和yas_odbc.dll在相同路径下。

   ```bash
   D:\yasdb-driver-odbc>dir
   2022/11/10  10:18    <DIR>          .
   2022/11/10  10:18    <DIR>          ..
   2022/11/09  05:43             1,041 install.bat
   2022/11/09  05:43               183 README.md
   2022/11/09  05:43               181 uninstall.bat
   2022/11/09  05:43            41,984 yasodbctest.exe
   2022/11/09  05:43           144,896 yas_odbc.dll
   ```

4. 运行install.bat，此操作需以管理员身份运行，如下：

   ![](./image/admin-install.png)

5. 成功后按任意键退出安装。

## 步骤4（可选）：检查依赖项（32位）

仅在安装32位驱动时，需要执行本操作。

1. 检查`C:\Windows\SysWOW64`文件夹中是否包含32位版本的ucrtbased.dll和vcruntime140d.dll。
   
   - 若有，则直接执行[配置数据源](#datasource)。
   
   - 若无，则需获取ucrtbased.dll和vcruntime140d.dll文件，并保存至`C:\Windows\SysWOW64`文件夹中。

2. 自行获取上述动态链接库文件，例如可以通过安装Visual Studio等方式获取。

<span id="datasource" name="datasource" class="yaslink"></span>

## 步骤5：配置数据源

通过Windows自带的ODBC数据源管理器进行YashanDB ODBC数据源配置。

1. 进入ODBC数据源管理器，添加用户数据源。

   - 64位：【控制面板 > 系统和安全 > 管理工具 > ODBC数据源管理程序（64位）】，此处以64位为例。

   - 32位：【控制面板 > 系统和安全 > 管理工具 > ODBC数据源管理程序（32位）】
   
   ![](./image/datasource.png)

​	仅当成功安装YashanDB ODBC驱动后才会出现YashanDB选项。

2. 填写数据源信息。

   ![](./image/connect.png)

   参数说明：

   - Data Source Name：ODBC数据源名称，唯一标识一个数据源 （必填项）

   - Description：数据源描述，等价于注释（选填项）

   - Server：数据源所在连接IP地址，即服务端IP地址（选填项）

   - Port：数据源端口，即服务端当前监听的IP端口（选填项）

   - Url：数据源的完整URL，请参考[C驱动yacConnect函数](../../C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect)中的URL参数，配置URL参数后，Server和Port参数将不会再生效（选填项）

   - User Name：当前连接用户（选填项）

   - PassWord：当前连接密钥（选填项）

   按钮说明：

   - OK：保存当前数据源信息，具体保存地址可见注册表：HKEY_CURRENT_USER\SOFTWARE\ODBC\ODBC.INI。

   - Cancel：取消当前所有修改。

   - Test Connection：测试当前连接，测试结果会弹出对话框提示。

3. 单击【OK】保存数据源信息，后续如需修改，单击【配置】即可修改当前数据源信息。

4. 单击【Test Connection】且测试连接正常后，表示安装完成，
   
   用户即可开始使用YashanDB ODBC驱动开发自己的客户端程序。
