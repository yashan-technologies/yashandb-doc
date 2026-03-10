## Package List

Please obtain the software packages from our technical support based on the required scenarios, which include the following four categories:

- YashanDB packages installed on the server by the DBA.
- Client software packages for connecting to YashanDB, installed locally by the users who need to use it.
- Various driver packages for developing YashanDB interface programs, selected for installation by application developers as needed.

The list of all packages provided by YashanDB is as follows: (xx.xx represents the software version number)

|Package Name |Description |
|-----------------------------| --------------------------------------------------------- |
| yashandb-xx.xx-linux-x86_64.tar.gz<br>yashandb-xx.xx-linux-aarch64.tar.gz<br/>yashandb-xx.xx-linux-loongarch64.tar.gz                                                  | YashanDB installation package, suitable for installing and deploying the database on Linux x86_64/ARM64/Loongson platforms. For installation guidance, refer to [YashanDB Server Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB服务端安装（命令行）/00YashanDB服务端安装（命令行）). |
| yashandb-image-personal-xx.xx-linux-x86_64.tar.gz<br>yashandb-image-personal-xx.xx-linux-aarch64.tar.gz                 | YashanDB image package, suitable for installing and deploying the database using Docker on Linux x86_64/ARM64 platforms. For installation guidance, refer to [YashanDB Server Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB服务端安装（Docker）). |
| yashandb-client-xx.xx-linux-x86_64.tar.gz<br>yashandb-client-xx.xx-linux-aarch64.tar.gz                                   | YashanDB client installation package, suitable for Linux x86_64/ARM64 platforms. For specific operations, refer to [Linux Client Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB客户端安装/Linux客户端安装). |
| yashandb-client-xx.xx-1.el7.centos.x86_64.rpm<br>yashandb-client-xx.xx-1.el7.aarch64.rpm                                  | YashanDB client installation package, suitable for CentOS Linux x86_64/ARM64 platforms. For installation guidance, refer to [Linux Client Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB客户端安装/Linux客户端安装). |
| yashandb-client-xx.xx-windows-amd64.zip                                                                                     | YashanDB client installation package, suitable for Windows platforms. For installation guidance, refer to [Windows Client Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB客户端安装/Windows客户端安装). |
| yashandb-odbc-xx.xx-linux-x86_64.tar.gz<br>yashandb-odbc-xx.xx-linux-aarch_64.tar.gz<br>yashandb-odbc-xx.xx-windows-x86_64.zip | YashanDB ODBC driver installation package, suitable for Linux x86_64/ARM64 and Windows platforms. For installation guidance, refer to [ODBC Driver Installation Instructions](../../All Manuals/开发手册/C语言系驱动/ODBC驱动/ODBC安装说明/00ODBC安装说明). |
| yashandb-oci-xx.xx-linux-x86_64.tar.gz<br>yashandb-oci-xx.xx-linux-aarch64.tar.gz<br>yashandb-oci-xx.xx-windows-amd64.zip | YashanDB OCI driver installation package, suitable for Linux x86_64/ARM64 and Windows platforms. For installation guidance, refer to [OCI Driver Installation Instructions](../../All Manuals/开发手册/C语言系驱动/OCI驱动/OCI安装说明/00OCI安装说明). |
| yaspy-xx.xx-xx-xx-linux-x86_64.whl<br>yaspy-xx.xx-xx-xx-linux-aarch_64.whl<br>yaspy-xx.xx-xx-xx-win_amd64.whl   | YashanDB Python driver installation package, suitable for Linux x86_64/ARM64 and Windows platforms. For installation guidance, refer to [Python Driver Installation Instructions](../../All Manuals/开发手册/Python驱动/安装Python驱动).<br/>You can choose either the yaspy series installation package or the yasdb installation package as needed; using the **yaspy** package is recommended. |
| yasdb-x.x.x-py3-none-any.whl   | YashanDB Python driver installation package, suitable for all platforms. For installation guidance, refer to [Python Driver Installation Instructions](../../All Manuals/开发手册/Python驱动/安装Python驱动). <br/>You can choose either the yaspy series installation package or the yasdb installation package as needed; using the **yaspy** package is recommended. |
| yashandb-jdbc-xx.xx.jar<br/>yashandb-jdbc-xx.xx-jre6.jar       | YashanDB JDBC driver installation package, suitable for all platforms. For installation guidance, refer to [JDBC Driver Installation Instructions](../../All Manuals/开发手册/JDBC驱动/安装JDBC驱动). |
| yashandb-dotnet-xx.xx.zip      | YashanDB ADO.NET driver installation package, suitable for all platforms. For installation guidance, refer to [ADO.NET Driver Installation Instructions](../../All Manuals/开发手册/ADO.NET驱动/ADO.NET驱动安装说明/00ADO.NET驱动安装说明). |
| yashandb-go-xx.zip            | YashanDB Go driver installation package, suitable for Linux x86_64/ARM64 and Windows platforms. For installation guidance, refer to [Go Driver Installation Instructions](../../All Manuals/开发手册/Go驱动/Go驱动安装说明/00Go驱动安装说明). |
| Ystream-xx.xx.jar   | Ystream client installation package, for usage instructions, refer to [Go Driver Installation Instructions](../../All Manuals/开发手册/YStream参考手册/YStream客户端使用介绍). |

<span id="Downloadingpackage" name="Downloadingpackage" class="yaslink"></span>

## Package Download

From this step onward, all subsequent server installation steps will be performed by the database installation user. Please switch to the yashan user or log in to the server directly as the yashan user.

For convenience in describing the YashanDB installation and deployment process, we will use the `yashandb-{version_number}-linux-x86_64.tar.gz` installation package as an example.

YashanDB supports downloading the software package on any server and initiating a parallel installation on all other servers. This installation manual takes 192.168.1.2 as the installation server, and all the following steps will be executed on 192.168.1.2.

```shell
# su yashan
```

**1. Create the install directory**

Create an empty directory under the yashan user's home directory to serve as the storage path for the installation package.

```shell
$ cd
$ mkdir install
```

**2. Download the software package**

Download the software package to the /home/yashan/install directory and extract it:

```shell
 $ cd /home/yashan/install
 $ tar zxf yashandb-{version_number}-linux-x86_64.tar.gz
```

**3. Obtain the installation tools**

The packages provided by YashanDB include installation tools located in the bin directory. Use the *yasboot* command for command-line installations, and the yasom command for visual installations.

```shell
$ ll ./bin/yasboot
-rwxrwxr-x 1 yashan yashan 9377280 Sep 26 01:32 yasboot
$ ll ./om/bin/yasom
-rwxrwxr-x 1 yashan yashan 9377280 Sep 26 01:32 yasom
```

After completing this step, you can enter the formal YashanDB installation process. Please choose the installation method based on your preferences:

- [Command-line installation](../YashanDB Installation via CLI/00YashanDB Installation via CLI): Log in directly to the server and execute each step of the installation in Linux command format.
- [Visual interface installation](../YashanDB Installation via GUI/00YashanDB Installation via GUI): ① Start the web service on the server; ② Execute each step of the installation through a web browser in the local Windows environment.