This article uses Centos 7.3.1 as an example to introduce the installation and configuration process of the YashanDB ODBC driver in this environment.

## Step 1: Install unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## Step 2: Download the C Driver Installation Package

1. Refer to the [YashanDB software package list](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB client installation package.

2. Download the YashanDB client installation package and extract it to a local path, for example, /home/yasdb-driver-c/.

   After extraction, the following folders can be obtained:

   - bin: Executable files for the C driver (currently includes *yasql*).

   - include: Header files for the C driver.

   - lib: Library files for the C driver.

## Step 3: Set Dynamic Library Dependency Path

Set `/home/yasdb-driver-c/lib` to the Linux LD_LIBRARY_PATH. The specific operations are:

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

## Step 4: Install ODBC Driver

1. Refer to the [YashanDB software package list](../../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Downloading Software Packages) to obtain the YashanDB ODBC driver installation package.

2. Download the ODBC driver installation package and extract it to a local path, for example, /home/yashandb_odbc/.

3. Install the ODBC driver (the so path is only a sample; please use the actual libyas_odbc.so path).

   ```shell
   vi /etc/odbcinst.ini

   [YashanDB]
   Description     = ODBC for YashanDB
   Driver          = /home/yashandb_odbc/libyas_odbc.so
   Setup           = /home/yashandb_odbc/libyas_odbc.so
   Driver64        = /home/yashandb_odbc/libyas_odbc.so
   Setup64         = /home/yashandb_odbc/libyas_odbc.so
   FileUsage       = 1
   ```

## Step 5: Configure Data Source

```shell
vi /etc/odbc.ini

[YASTEST]
Description  = YashanTest
Driver       = YashanDB
SERVER       = 127.0.0.1
PORT         = 1688
URL          = 127.0.0.1:1688
USER         = sys
PWD          = sys
```

The URL in the configuration is the complete URL of the data source. Please refer to the [C driver yacConnect function](../../C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) for the url parameter. After configuring the URL parameter, the SERVER and PORT parameters will no longer take effect.

Charset settings can be made here (provided that the ANSI driver is used; the default ANSI ODBC charset in Linux is UTF8). The following data source configuration can be used to set the charset:

```shell
# Currently supported charsets include: ASCII, ISO88591, GBK, UTF8, UTF16, GB18030
CHARACTER_SET = UTF8
```

## Step 6: Test Connection

```shell
# yastest is the name of the data source
isql yastest -v
# View data source names
odbcinst -q -s
# View driver names
odbcinst -q -d
# View driver configuration
odbcinst -j
```

## Step 7: (Optional) Test Connection Using the Built-in Test Program of yasodbc

Execute yasodbctest, where yastest represents the data source name.

```shell
./yasodbctest yastest sys sys
# Or
./yasodbctest yastest
```

Use exit to quit.

![](./image/yasodbctest.png)
