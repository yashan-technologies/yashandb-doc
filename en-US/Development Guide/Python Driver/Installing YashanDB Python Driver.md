The YashanDB Python driver supports both Windows and Linux platforms. This chapter will describe the installation and configuration process of YashanDB Python driver on these two platforms.

## Step 1: Prepare Python Environment

Verify Python Environment and pip. If not installed, please download and install.

First verify whether Python and pip are installed. Installing YashanDB Python driver requires pip. Under normal circumstances, pip is already built into Python and does not need to be installed again.

If Python is not installed yet, please download and install Python version Python 3.6 or above from the [Python Official Website](https://www.python.org/downloads/), or contact technical personnel to obtain the installation package and configure the Python environment yourself.

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

Using the YashanDB Python driver requires the YashanDB C driver library to be installed and configured first. The installation files for the YashanDB C driver are integrated into the YashanDB client installation package, and the corresponding client installation package must be obtained during installation.

### Step 2.1: Download the C Driver Installation Package

::: tabs
== Windows

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 
2. Download and extract the driver package named `YashanDB Connector/C xxx` and marked as Windows to a local path, such as `D:\yasdb-driver-c\`.

   After extracting the installation package, the `yascli.dll` file can be found in the lib folder:


== Linux

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 
2. Download and extract the driver package named `YashanDB Connector/C xxx` and marked as Linux to a local path, such as `/home/yasdb-driver-c/lib`.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.

:::

### Step 2.2: Set Up Dynamic Library Dependency Path

::: tabs
== Windows

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.

== Linux

1. Edit the bashrc file:

   ```shell
   $ vi ~/.bashrc
   ```

2. Add a new line to increase the LD_LIBRARY_PATH search path pointing to the folder where the C driver's library files are located:

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. Save and exit.

4. Load bash configuration to make the modifications take effect.

   ```shell
   $ source ~/.bashrc
   ```
:::

## Step 3: Install YashanDB Python Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

    > **Note**:
    >
    > If you choose to use the yaspy driver package, the `cp3x-cp3xm` in the wheel filename must match the local Python version (check via `python --version`, for example Python 3.12 corresponds to `cp312-cp312`). For Windows platform, it is recommended to select `yaspy-xx.xx-cp3x-cp3xm-win_amd64.whl`. For Linux platform, it is recommended to select `yaspy-xx.xx-cp3x-cp3xm-linux_x86_64.whl` or `yaspy-xx.xx-cp3x-cp3xm-linux_aarch_64.whl`.

2. Download the compressed package to a local path, such as /path/YASDB Python.

3. Install the driver package by running the command pip3 install *package_name*.

    ::: tabs
    == Windows

```bash
C:\>pip3 install yaspy-xx.xx-cp312-cp312-win_amd64.whl
```

    == Linux

> **Note**:
>
> If installing the driver system-wide on Linux, you may need to switch to root user. When using a virtual environment, this is usually not required.

```shell
# pip3 install yaspy-xx.xx-cp3x-cp3xm-linux_x86_64.whl
```
    :::

Once installation is successful, the Python application can access YashanDB by referencing the yaspy module.
