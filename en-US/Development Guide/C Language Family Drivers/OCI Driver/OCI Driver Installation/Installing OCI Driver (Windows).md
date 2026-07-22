This article takes Windows 10 Professional Edition as an example to introduce the installation and configuration process of the YashanDB OCI driver in this environment.

> **Note**:
>
> The YashanDB OCI driver relies on the C basic development library, so the application side also needs to install C compilation tools, such as gcc, Visual Studio, etc.

## Step 1: Install YashanDB C Driver

Using YashanDB OCI driver requires first installing YashanDB C driver and setting environment variables. The YashanDB C driver installation files are integrated in the YashanDB client installation package, and the corresponding client installation package needs to be obtained during installation.

### Step 1.1: Download the C Driver Installation Package
1.From the YashanDB Official Website Download Center (opens new window)or contact our technical support to obtain the corresponding software package.

2.Download and extract the YashanDB client installation package to a local path, such as D:\yasdb-driver-c.

After extraction, the following folders can be found:

   * bin: Executable files for the C driver.

   * include: Header files for the C driver.

   * lib: Library files for the C driver.

### Step 1.2: Set Environment Variables
Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1.Right-click the "This PC" icon on the desktop and select [Properties].

2.Click on [Advanced system settings].

3.Click on [Environment Variables].

4.In the [System variables] area, select the [Path] item and click the [Edit] button below.

5.Click [New] and enter the folder where the C driver's library files are located, for example, D:\yasdb-driver-c\lib.

6.Click [OK] to save the configuration.


## Step 2: Download the OCI Driver Installation Package and Header Files

### Step 2.1: Download the OCI Installation Package

1. Obtain the corresponding software package from the [YashanDB Official Download Center](https://download.yashandb.com/download) or contact our technical support.

2. Download the OCI driver installation package and extract it to the local path `D:\yasdb-driver-c\lib`.

   After extracting the zip file, yas_oci.dll is the OCI dynamic library. yas_oci.dll depends on yascli.dll, and yascli.dll depends on yas_infra.dll.

   * If using a CMake project, only yas_oci.dll is required as a dependency.
   * If using other linking tools, add dependencies as needed.

### Step 2.2: Download the Header Files

1. Download the Instant Client Basic Package from the [Oracle Official Website](https://www.oracle.com/database/technologies/instant-client/downloads.html) to obtain `oci.h`, `oratypes.h`, and other related header files. After downloading, extract them to any directory, for example, `D:\yasdb-driver-c\lib`.

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

## Step 3: Configure Data Source

Before connecting, ensure that the YASDB_HOME system path exists in the system and that the data source has been configured in the yasc_service.ini file.

1. Navigate to the directory where the data source is located: %YASDB_HOME%\client\yasc_service.ini.
   
2. Configure the data source in the yasc_service.ini file, for example, `CITEST = 127.0.0.1:1688`.

## Step 4: Test Connection

The yasociconntest is a test executable file to check whether the OCI connection is established.

## Step 5: (Optional) Enable Logging

Before enabling logging, ensure that the YASDB_HOME system path exists and that there is a client directory under the YASDB_HOME system path.

> **Note**:
>
> Log enabling may lead to OCI performance loss. Use with caution.

**Log Path**

```c
%YASDB_HOME%\client\yasoci.log
```

**Logging Method**

Method 1: Set the system variable in the environment.

```c
YASOCI_LOG = ON
```

Method 2: Set the process-level system variable in the code.

```c
(void)putenv("YASOCI_LOG=ON");
```
