This article uses Centos 7.3.1, the OCI package yashandb-oci-xx.xx-linux-x86_64.tar.gz, and the YashanDB client installation package yashandb-client-xx.xx-linux-x86_64.tar.gz as an example to introduce the installation and configuration process of the YashanDB OCI driver in this environment.

## Step 1: Download C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

2. Download and extract the YashanDB client installation package to a local path, such as /home/yasdb-driver-c/lib.

   After extraction, the following folders can be found:

   * bin: Executable files for the C driver.

   * include: Header files for the C driver.
   
   * lib: Library files for the C driver.

## Step 2: Download the OCI Driver Installation Package and Header Files

### Step 2.1: Download the OCI Installation Package

1. Obtain the corresponding software package from the [YashanDB Official Download Center](https://download.yashandb.com/download) or contact our technical support.

2. Download the OCI driver installation package and extract it to the local path `/home/yasdb-driver-c/lib`.

   After extraction, libyas_oci.so is the OCI dynamic library. libyas_oci.so depends on libyascli.so, and libyascli.so depends on libyas_infra.so.

   * If using a CMake project, only libyas_oci.so is required as a dependency.
   * If using gdb for linking, libyas_oci.so, libyascli.so, and libyas_infra.so are all required as dependencies.

### Step 2.2: Download the Header Files

1. Download the Instant Client Basic Package from the [Oracle Official Website](https://www.oracle.com/database/technologies/instant-client/downloads.html) to obtain `oci.h`, `oratypes.h`, and other related header files. After downloading, extract them to any directory, for example, `/home/yasdb-driver-c/lib`.

2. Compile and run the sample program:

```shell
# Set the Oracle Instant Client path (containing oci.h and oratypes.h header files)
export YASHAN_HOME=/home/yasdb-driver-c/lib

# Compile the OCI sample program
gcc -o ociexample ociexample.c -I${YASHAN_HOME}/sdk/include -L${YASHAN_HOME} -lclntsh -lpthread -ldl

# Run the program (requires setting LD_LIBRARY_PATH)
export LD_LIBRARY_PATH=${YASHAN_HOME}:$LD_LIBRARY_PATH
./ociexample
```

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
