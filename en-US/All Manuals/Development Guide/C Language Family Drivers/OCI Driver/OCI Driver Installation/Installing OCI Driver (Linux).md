This article uses Centos 7.3.1, the OCI package yashandb-oci-xx.xx-linux-x86_64.tar.gz, and the YashanDB client installation package yashandb-client-xx.xx-linux-x86_64.tar.gz as an example to introduce the installation and configuration process of the YashanDB OCI driver in this environment.

## Step 1: Download C Driver Installation Package



1. Refer to the [YashanDB Package List](../../../../安装和升级/安装部署/安装前准备/下载软件包) to obtain the YashanDB client installation package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



## Step 2: Download OCI Driver Installation Package

1. Refer to the [YashanDB Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB OCI driver installation package.

2. Download and extract the OCI driver installation package to the local path `/home/yasdb-driver-c/lib`.

   The extracted libyas_oci.so is the oci dynamic library, libyas_oci.so depends on libyascli.so, and libyascli.so depends on libyas_infra.so.
   * If using a cmake project, only libyas_oci.so is required.

   * If using gdb linkage, it requires libyas_oci.so, libyascli.so, and libyas_infra.so.

## Step 3: Set Dynamic Library Dependency Path and Data Source Environment Variable

Set `/home/yasdb-driver-c/lib` in the Linux LD_LIBRARY_PATH. The specific operations are:

1. Edit the bashrc file:

   ```shell
   vi ~/.bashrc
   ```

2. Add a new line to increase the LD_LIBRARY_PATH search path pointing to the folder where the C driver's library files are located:

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. (Optional) When the client and server are not on the same server, you also need to configure the data source information path as a global variable, and the variable name must be YASDB_HOME:

   ```shell
   export YASDB_HOME=/home/yasdb-driver-c/lib
   ```
4. Save and exit.

5. Refresh the system variable configuration.

   ```shell
   source ~/.bashrc
   ```

## Step 4: Configure Data Source

1. Create data source information file directory:

   ```shell
   cd $YASDB_HOME
   mkdir client
   cd client
   ```

2. Configure the data source information in the yasc_service.ini file. The configuration parameter is `DBServer_IPaddr:port`. The default port is 1688, for example:

   ```shell
   vi yasc_service.ini
   
   CITEST = 192.168.1.10:1688
   ```

   In this example, the data source name is CITEST. When multiple database service information is configured, the application can connect to the corresponding server through this name when connecting.

## Step 5: Test Connection

yasociconntest is a test executable file. Execute this file to test if the OCI connection is established.
```shell
./yasociconntest
```

## Step 6: (Optional) Enable Logging

Before enabling logging, ensure that the YASDB_HOME system path exists and the client directory exists under the YASDB_HOME system path.

> **Note**:
>
> Log enabling may lead to OCI performance loss. Use with caution.

**Path of the log**

```c
$YASDB_HOME/client/yasoci.log
```

**Method to enable logging**

Method 1: Set a system variable in the environment.

```c
export YASOCI_LOG = ON
```

Method 2: Set a process-level system variable in the code.

```c
(void)putenv("YASOCI_LOG=ON");
```
