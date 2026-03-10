## Step 1: Prepare Python Environment

To connect to YashanDB using a generic Python application, you need to first install and configure the Python development environment. The YashanDB Python driver supports Python versions 3.6.0 and above.

The YashanDB Python driver supports both Windows and Linux platforms. This manual will describe the installation and configuration process of the YashanDB Python driver for these two platforms.

1. Download and Install Python Development Environment.

    Download and install a compatible Python package from the official source as per the above requirements, and configure the Python environment accordingly.

2. Verifying Python Environment and pip.

    Installing YashanDB Python driver requires pip. Under normal circumstances, pip is already built into Python and does not need to be installed again.

    ::: tabs
    == Windows

In Windows system, Python environment can be verified by executing python --version command, and pip installation can be verified by executing pip3 --version command.

```bash
C:\>python --version
3.6.15
C:\>pip3 --version
```

    == Linux

In Linux system, Python environment can be verified by executing python3 --version command, and pip installation can be verified by executing pip3 --version command.

```shell
# python3 --version
# Python 3.6.15
# pip3 --version
```
    :::

## Step 2: Install YashanDB C Driver

> **Note**:
> 
> Using the YashanDB Python driver requires the YashanDB C driver library to be installed and configured first. The installation files for the YashanDB C driver are integrated into the YashanDB client installation package, and the corresponding client installation package must be obtained during installation.

::: tabs
== Windows


### Step 1: Download the C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the YashanDB client installation package to a local path, such as D:\yasdb-driver-c\.

   After extraction, the following folders can be found:

   * bin: Executable files for the C driver.

   * include: Header files for the C driver.
   
   * lib: Library files for the C driver.

### Step 2: Set Environment Variables

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.



== Linux



### Step 1: Download the C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * bin: Executable files for the C driver (currently includes *yasql*).

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



:::

## Step 3: Install YashanDB Python Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

> **Note**:
>
> If you choose to use the yaspy driver package, the Windows platform should select yaspy-xx.xx-cp36-cp36m-win_amd64.whl, while the Linux platform should select yaspy-xx.xx-cp36-cp36m-linux-x86_64.whl or yaspy-xx.xx-cp36-cp36m-linux-aarch_64.whl.

2. Download the compressed package to a local path, such as /path/YASDB Python.

3. Install the driver package by running the command pip3 install *package_name*.

    ::: tabs
    == Windows

```bash
C:\>pip3 install yaspy-1.0.0-cp38-cp38-win_amd64.whl
```

    == Linux

> **Note**: 
>
> When installing the driver on the Linux platform, you need to switch to the root user first.

```shell
# pip3 install yaspy-1.0.0-cp36-cp36m-linux_x86_64.whl
```
    :::

Once installation is successful, the Python application can access YashanDB by referencing the yaspy module.
