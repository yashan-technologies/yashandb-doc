## Python Environment Preparation

To connect to YashanDB using a generic Python application, you need to first install and configure the Python development environment. The YashanDB Python driver supports Python versions 3.6.0 and above.

The YashanDB Python driver supports both Windows and Linux platforms. This manual will describe the installation and configuration process of the YashanDB Python driver for these two platforms.

**Download and Install Python Development Environment**

Download and install a compatible Python package from the official source as per the above requirements, and configure the Python environment accordingly.

**Verifying Python Environment on Linux Platform**

After configuration, verify if the Python environment is functioning properly by running python3 --version:

```shell
# python3 --version
Python 3.6.15
```

**Verifying Python Environment on Windows Platform**

After configuration, verify if the Python environment is functioning properly by running python --version:

```bash
C:\>python --version
3.6.15
```

The installation of the YashanDB Python driver package requires pip. Normally, pip is already built into Python, so there is no need to install it again. You can verify if pip is installed on both platforms by running pip3 --version:

```shell
pip3 --version
```

## Installing Dependencies

To use the YashanDB Python driver, you need to first install and configure the YashanDB client to obtain the YashanDB C driver library. Please refer to the installation manual for the installation steps in the [YashanDB Client Installation](../../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) section.

## Installing YashanDB Python Driver Package

1. Obtain the platform-specific software package according to the installation manual [YashanDB Software Package List](../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages). It is recommended to select yasdb-*version_number*-py3-none-any.whl. This manual takes version 1.0.2 as an example.

> **Note**:
>
> If you choose to use the yaspy driver package, the Windows platform should select yaspy-xx.xx-cp36-cp36m-win_amd64.whl, while the Linux platform should select yaspy-xx.xx-cp36-cp36m-linux-x86_64.whl or yaspy-xx.xx-cp36-cp36m-linux-aarch_64.whl.

2. Download the compressed package to a local path, such as /path/YASDB Python.

3. Both platforms can install the driver package by running the command pip3 install *package_name*.

**Installing Driver on Linux Platform**

```bash
pip3 install yasdb-1.0.2-py3-none-any.whl
```

> **Note**: 
>
> When installing the driver on the Linux platform, you need to switch to the root user first.

**Installing Driver on Windows Platform**

```shell
pip3 install yasdb-1.0.2-py3-none-any.whl
```

Once installation is successful, the Python application can access YashanDB by referencing the yasdb module.