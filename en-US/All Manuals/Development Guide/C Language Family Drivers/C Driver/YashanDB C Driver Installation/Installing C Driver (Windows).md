This document takes Windows 10 Professional, CMake version 3.16 and above, and the YashanDB client installation package yashandb-client-xx.xx-windows-amd64.zip as an example to introduce the installation and configuration process of the YashanDB C driver in this environment.



## Step 1: Download the C Driver Installation Package



1. Refer to the [YashanDB Package List](../../../../安装和升级/安装部署/安装前准备/下载软件包) to obtain the YashanDB client installation package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



## Step 2: Set Environment Variables

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.



At this point, the YashanDB C driver has been successfully installed.

##  Step 3 (Optional): Create a New C/C++ Project

The following example project requires a CMake environment and any IDE that supports CMake projects.

1. Create a project folder yacli_c_project at any path.

2. Create a file CMakeLists.txt under yacli_c_project.

3. Modify the CMakeLists.txt file with the following content:
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
4. Copy the "Usage Example" code from the corresponding document in the [C Driver Usage Examples](../C驱动使用示例说明/00C驱动使用示例说明) directory (for example, the document on [C Driver Connecting and Disconnecting](../C驱动使用示例说明/C驱动连接与断开)), and save it as a file named cexample.c in the current project path.

   The directory structure of yacli_c_project is as follows:

   * CMakeLists.txt

   * cexample.c
   
5. Open the current folder with any IDE and compile and run yasctest.
