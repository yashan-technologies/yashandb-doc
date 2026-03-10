This article uses Centos 7.3.1, cmake version 3.16 or above, and the YashanDB client installation package yashandb-client-xx.xx-linux-x86_64.tar.gz as an example to introduce the installation and configuration process of the YashanDB C driver in this environment.

## Step 1: Download the C Driver Installation Package

1. Refer to the [YashanDB Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB client installation package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the following folders can be found:

   * bin: Executable files for the C driver (currently includes *yasql*).

   * include: Header files for the C driver.
   
   * lib: Library files for the C driver.

## Step 2: Set Up Dynamic Library Dependency Path

1. Edit the bashrc file:

   ```shell
   vi ~/.bashrc
   ```

2. Add a new line to increase the LD_LIBRARY_PATH search path:

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. Save and exit.

4. Refresh the system variable configuration.
   ```shell
   source ~/.bashrc
   ```

At this point, the YashanDB C driver has been successfully installed. For character set-related configuration adjustments, please refer to [Character Set Configuration](../../../../Database Administration/Basic Database Management/Character Set Configuration).

## (Optional) Step 3: Create a C/C++ Language Project

The following example project depends on the cmake environment and requires the cmake component to be installed in advance.

1. Create a project folder yacli_c_project in any path.

2. Create a file CMakeLists.txt in yacli_c_project.

3. Modify the CMakeLists.txt file to contain the following content and save:

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

4. Copy the cexample.c from the [YashanDB C Driver Usage Examples](../C Driver Usage Examples/00C Driver Usage Examples) to the current project path.

   The directory structure of yacli_c_project is as follows:
   
   * CMakeLists.txt

   * cexample.c

5. Generate the cmake project in the current path:

   ```shell
   cmake .
   ```

6. Compile in the current path:

   ```shell
   make
   ```

7. Run yasctest:

   ```shell
   ./yasctest
   ```
