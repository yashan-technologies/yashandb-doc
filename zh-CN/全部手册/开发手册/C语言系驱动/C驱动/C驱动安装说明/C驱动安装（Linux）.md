本文以使用Centos 7.3.1、cmake3.16及以上版本和YashanDB客户端安装包yashandb-client-xx.xx-linux-x86_64.tar.gz为例，介绍YashanDB C驱动在该环境下的安装配置过程。

## 步骤1：下载C驱动安装包

1. 参考[YashanDB软件包清单](../../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yasdb-driver-c/。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件（目前包括yasql）。

   * include：C驱动的头文件。
   
   * lib：C驱动的库文件。

## 步骤2：设置动态库依赖路径

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

到此YashanDB C驱动就已安装完毕。如需调整字符集相关配置，请参考[字符集配置](../../../../数据库管理/基本数据库管理/字符集配置)。

## （可选）步骤3：新建C/C++语言工程

以下示例工程依赖cmake环境，需要提前安装cmake组件。

1. 在任意路径新建工程文件夹yacli_c_project。

2. 在yacli_c_project下新建文件CMakeLists.txt。

3. 修改CMakeLists.txt文件为以下内容并保存：

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
   target_link_libraries(yasctest /home/yasdb-driver-c/lib/libyascli.so)
   target_include_directories(yasctest PUBLIC /home/yasdb-driver-c/include)
   ```

4. 拷贝[YashanDB C驱动使用示例](../C驱动使用示例说明/00C驱动使用示例说明)中的cexample.c至当前工程路径。

   yacli_c_project的目录结构如下：
   
   * CMakeLists.txt

   * cexample.c

5. 在当前路径下生成cmake工程：

   ```shell
   cmake .
   ```

6. 在当前路径下编译：

   ```shell
   make
   ```

7. 运行yasctest：

   ```shell
   ./yasctest
   ```
