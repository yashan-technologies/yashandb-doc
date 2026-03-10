本章将介绍YashanDB的C驱动在Windows10专业版、cmake3.16及以上版本平台下的安装配置过程以及工程代码示例。

## 对接前准备

1. 参考[快速体验YashanDB](快速体验YashanDB)获取YashanDB客户端安装包。

2. 将YashanDB客户端安装包下载并解压到本地路径，例如`D:\yasdb-driver-c\`。

   安装包解压后可得到以下文件夹：

   * bin：C驱动的可执行文件。

   * include：C驱动的头文件。

   * lib：C驱动的库文件。

3. 右键单击【此电脑】，依次进入【属性 > 高级系统设置 > 环境变量 > 系统变量 > Path】，单击【编辑】并新建页面，配置C驱动库文件路径`D:\yasdb-driver-c\lib`，保存配置后即可完成C驱动安装。

## C/C++工程对接示例

1. 在现有C/C++代码工程下新建文件CMakeLists.txt，将C驱动相关文件配置信息参考如下示例完成配置：

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

2. 在CMakeLists.txt相同路径下创建数据库连接与断开的代码文件example.cpp，根据实际YashanDB服务器IP端口信息及连接用户名密码配置连接信息，示例如下：

   ```c
   #include <string.h>
   #include "yacli.h"
   
   
   #define TEST_CALL(proc)                           \
       do {                                         \
           if ((YacResult)(proc) != YAC_SUCCESS) {  \
               return YAC_ERROR;                    \
           }                                        \
       } while (0)
   
   void printError()
   {
       YacInt32   code;
       YacChar    msg[1000];
       YacTextPos pos;
   
       yacGetDiagRec(&code, msg, 1000, NULL, NULL, 0, &pos);
       if (pos.line != 0) {
           printf("[%d:%d]", pos.line, pos.column);
       }
       printf("YAC-%05d %s\n", code, msg);
   }
   
   typedef struct {
       YacHandle env;
       YacHandle conn;
       YacHandle stmt;
   } YacTestEnv;
   
   YacTestEnv gTestEnv = { 0 };
   
   // 连接数据库
   YacResult testConnect()
   {
       // 更改为实际数据库服务器的IP和端口
       const YacChar* gSrvStr = "192.168.1.2:1688";
       const YacChar* user = "yashan";
       const YacChar* pwd = "yashan";
   
       TEST_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
       TEST_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
       TEST_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
       TEST_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));
   
       return YAC_SUCCESS;
   }
   // 断开数据库
   YacResult testDisConnect()
   {
       TEST_CALL(yacFreeHandle(YAC_HANDLE_STMT, gTestEnv.stmt));
       yacDisconnect(gTestEnv.conn);
       TEST_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
       TEST_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));
   
       return YAC_SUCCESS;
   }
   
   YacResult cexample()
   {
       TEST_CALL(testConnect());
       TEST_CALL(testDisConnect());
   
       return YAC_SUCCESS;
   }
   
   int main()
   {
       if (cexample() == YAC_SUCCESS) {
           printf("cexample succeed!\n");
       } else {
           printError();
           printf("cexample failed!\n");
       }
       return 0;
   }
   ```

上述示例中共配置了如下C接口：

|  函数| 说明|
| ------------------------------------------------------------ | ---------------------- |
| [yacAllocHandle](../开发手册/C语言系驱动/C驱动/C驱动接口说明/句柄和描述符函数/yacAllocHandle) | 为句柄分配空间         |
| [yacFreeHandle](../开发手册/C语言系驱动/C驱动/C驱动接口说明/句柄和描述符函数/yacFreeHandle) | 释放句柄空间           |
| [yacConnect](../开发手册/C语言系驱动/C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect) | 客户端发起连接请求     |
| [yacDisconnect](../开发手册/C语言系驱动/C驱动/C驱动接口说明/连接、授权和初始化函数/yacDisconnect) | 客户端发起断开连接请求 |

3. 当前文件夹下，使用任意IDE编译或者直接打开CMD按照如下使用CMAKE编译，编译运行yasctest。

   ```bash
   cmake .
   MSBuild .\example.sln
   .\Debug\yasctest.exe
   ```

如需了解更多的C接口信息以及应用示例，可参考[C驱动说明](../开发手册/C语言系驱动/C驱动/00C驱动)。
