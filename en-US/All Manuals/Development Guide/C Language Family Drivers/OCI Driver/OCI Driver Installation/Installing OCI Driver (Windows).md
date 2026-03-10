This article takes Windows 10 Professional Edition as an example to introduce the installation and configuration process of the YashanDB OCI driver in this environment.

> **Note**:
>
> The YashanDB OCI driver relies on the C basic development library, so the application side also needs to install C compilation tools, such as gcc, Visual Studio, etc.

## Step 1: Download C Driver Installation Package

1. Refer to the [YashanDB Software Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB client installation package.

2. Download the YashanDB client installation package and extract it to a local path, such as `D:\yasdb-driver-c`.

   After extracting the installation package, the following directories will be obtained:

   * bin: Executable files for the C driver.

   * include: Header files for the C driver.

   * lib: Library files for the C driver.

## Step 2: Set Environment Variables

Set `D:\yasdb-driver-c\lib` in the Windows environment variable PATH. The specific operations are as follows:

1. Right-click the "This PC" icon on the desktop and select "Properties".

2. Click on "Advanced system settings".

3. Click on "Environment Variables".

4. In the "System variables" area, select the "Path" item and click "Edit" below.

5. Click "New" and enter `D:\yasdb-driver-c\lib`.

6. Click "OK" to save the configuration.

## Step 3: Download OCI Installation Package

1. Refer to the [YashanDB Software Package List](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB OCI driver installation package.

2. Download the OCI driver installation package and extract it to the local path `D:\yasdb-driver-c\lib`.

   The yas_oci.dll obtained after unzipping the zip file is the OCI dynamic library, and yas_oci.dll depends on yascli.dll, while yascli.dll depends on yas_infra.dll.

   * If using a cmake project, only yas_oci.dll is required.

   * If using other linking tools, please depend as needed.

## Step 4: Configure Data Source

Before connecting, ensure that the YASDB_HOME system path exists in the system and that the data source has been configured in the yasc_service.ini file.

1. Navigate to the directory where the data source is located: %YASDB_HOME%\client\yasc_service.ini.
   
2. Configure the data source in the yasc_service.ini file, for example, `CITEST = 127.0.0.1:1688`.

## Step 5: Test Connection

The yasociconntest is a test executable file to check whether the OCI connection is established.

## Step 6: (Optional) Enable Logging

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
