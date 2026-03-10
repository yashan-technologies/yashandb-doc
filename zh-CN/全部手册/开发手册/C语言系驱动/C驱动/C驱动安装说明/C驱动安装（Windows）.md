本文以Windows10专业版、cmake3.16及以上版本和YashanDB客户端安装包yashandb-client-xx.xx-windows-amd64.zip为例，介绍YashanDB C驱动在该环境下的安装配置过程。



## 步骤1：下载C驱动安装包



1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yasdb-driver-c/。

   安装包解压后可得到C驱动所需文件：

   * C驱动的头文件：位于include文件夹中。

   * C驱动的库文件：位于lib文件夹中。



## 步骤2：设置环境变量

将C驱动的库文件所在文件夹设置到Windows环境变量PATH中，具体操作为：

1. 右键单击系统桌面上的【此电脑】图标，选择【属性】。

2. 单击【高级系统设置】。

3. 单击【环境变量】。

4. 在【系统变量】区域，选中【Path】项，单击下方的【编辑】。

5. 单击【新建】，输入C驱动的库文件所在文件夹，例如`D:\yasdb-driver-c\lib`。

6. 单击【确定】保存配置。



至此，YashanDB C驱动安装完成。

## 步骤3（可选）：新建C/C++语言工程

以下示例工程依赖cmake环境以及任意支持cmake工程的IDE。

1. 在任意路径新建工程文件夹yacli_c_project。

2. 在yacli_c_project下新建文件CMakeLists.txt。

3. 修改CMakeLists.txt文件为以下内容：
   ```cmake
   cmake_minimum_required(VERSION 3.16)
   project(example)

   set(CMAKE_CXX_STANDARD 11)
   set(CMAKE_CXX_STANDARD_REQUIRED ON)
   set(BUILD_USE_64BITS on)
   set(CMAKE_CONFIGURATION_TYPES "Debug" CACHE STRING "" FORCE)
   set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "" FORCE)

   # set src code
   SET(LIBHELLO_SRC 
   ./cexample.c
   )

   add_executable(yasctest ${LIBHELLO_SRC})
   target_link_libraries(yasctest D:/yasdb-driver-c/lib/yascli.lib)
   target_include_directories(yasctest PUBLIC D:/yasdb-driver-c/include)
   ```
4. 拷贝[C驱动使用示例说明](../C驱动使用示例说明/00C驱动使用示例说明)目录下相应场景（例如[C驱动连接与断开](../C驱动使用示例说明/C驱动连接与断开)）文档中的“使用示例”代码，保存为文件至当前工程路径，命名为cexample.c。

   yacli_c_project的目录结构如下：

   * CMakeLists.txt

   * cexample.c
   
5. 使用任意IDE打开当前文件夹，编译运行yasctest。
