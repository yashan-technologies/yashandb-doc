This chapter will introduce the installation and configuration process of the YashanDB C driver on the Windows 10 Professional Edition platform with cmake version 3.16 and above, along with example project code.

## Preparation Before Integration

1. Refer to [Quick Start with YashanDB](./Quick Start with YashanDB) to obtain the YashanDB client installation package.

2. Download the YashanDB client installation package and extract it to a local path, such as `D:\yasdb-driver-c\`.

   After extracting the installation package, the following folders will be obtained:

   * bin: Executable files of the C driver.

   * include: Header files of the C driver.

   * lib: Library files of the C driver.

3. Right-click on "This PC", proceed to [Properties > Advanced System Settings > Environment Variables > System Variables > Path], click [Edit], and create a new entry to configure the C driver library file path `D:\yasdb-driver-c\lib`. After saving the configuration, the C driver installation will be complete.

## C/C++ Project Integration Example

1. Create a new file named CMakeLists.txt under the existing C/C++ code project, and configure the C driver related file configuration information as shown in the following example:

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

2. Create a code file named example.cpp in the same path as CMakeLists.txt to connect and disconnect from the database. Configure the connection information based on the actual YashanDB server IP and port information, as shown in the example below:

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
   
   // Connect to the database
   YacResult testConnect()
   {
       // Change to the actual database server IP and port
       const YacChar* gSrvStr = "192.168.1.2:1688";
       const YacChar* user = "yashan";
       const YacChar* pwd = "yashan";
   
       TEST_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
       TEST_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
       TEST_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
       TEST_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));
   
       return YAC_SUCCESS;
   }
   // Disconnect from the database
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

The above example configures the following C interfaces:

|Function |Description |
| ------------------------------------------------------------ | ---------------------- |
| [yacAllocHandle](../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Handle and Descriptor Functions/yacAllocHandle) | Allocate space for a handle |
| [yacFreeHandle](../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Handle and Descriptor Functions/yacFreeHandle) | Free space for a handle   |
| [yacConnect](../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) | Initiate connection request from the client |
| [yacDisconnect](../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacDisconnect) | Initiate disconnect request from the client |

3. In the current folder, use any IDE to compile, or directly open CMD and compile using CMAKE as follows to run yasctest.

   ```bash
   cmake .
   MSBuild .\example.sln
   .\Debug\yasctest.exe
   ```

For more information about C interfaces and application examples, please refer to [C driver documentation](../All Manuals/Development Guide/C Language Family Drivers/C Driver/00C Driver).