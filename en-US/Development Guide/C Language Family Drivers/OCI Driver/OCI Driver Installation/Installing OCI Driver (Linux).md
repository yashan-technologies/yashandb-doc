This article uses Centos 7.3.1, the OCI package yashandb-oci-xx.xx-linux-x86_64.tar.gz, and the YashanDB client installation package yashandb-client-xx.xx-linux-x86_64.tar.gz as an example to introduce the installation and configuration process of the YashanDB OCI driver in this environment.

## Step 1: Download C Driver Installation Package

1. Refer to the [YashanDB Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB client installation package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extraction, the following folders will be obtained:

   * bin: Executable files of C driver (currently includes *yasql*).

   * include: Header files of C driver.

   * lib: Library files of C driver.

## Step 2: Set Dynamic Library Dependency Path

Set `/home/yasdb-driver-c/lib` in the Linux LD_LIBRARY_PATH. The specific operations are:

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

## Step 3: Download OCI Driver Installation Package

1. Refer to the [YashanDB Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB OCI driver installation package.

2. Download and extract the OCI driver installation package to the local path `/home/yasdb-driver-c/lib`.

   The extracted libyas_oci.so is the oci dynamic library, libyas_oci.so depends on libyascli.so, and libyascli.so depends on libyas_infra.so.
   * If using a cmake project, only libyas_oci.so is required.

   * If using gdb linkage, it requires libyas_oci.so, libyascli.so, and libyas_infra.so.

## Step 4: Configure Data Source

Before connecting, ensure that the YASDB_HOME system path exists and the data source is configured in the yasc_service.ini file.

1. Navigate to the data source location:
   ```shell
   cd $YASDB_HOME/client/
   ```

2. Configure the data source in the yasc_service.ini file, for example:
   ```shell
   vi yasc_service.ini
   
   CITEST = 192.168.1.10:1688
   ```

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
