This article uses Centos 7.3.1 as an example to introduce the installation and configuration process of the YashanDB ODBC driver in this environment.

## Step 1: Install unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## Step 2: Install YashanDB C Driver



### Step 1: Download the C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * bin: Executable files for the C driver (currently includes *yasql*).

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



## Step 3: Install YashanDB ODBC Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download the ODBC driver installation package and extract it to a local path, for example, /home/yashandb_odbc/.

3. Install the ODBC driver (the so path is only a sample; please use the actual libyas_odbc.so path).

   ```shell
   $ vi /etc/odbcinst.ini

   [YashanDB]
   Description     = ODBC for YashanDB
   Driver          = /home/yashandb_odbc/libyas_odbc.so
   Setup           = /home/yashandb_odbc/libyas_odbc.so
   Driver64        = /home/yashandb_odbc/libyas_odbc.so
   Setup64         = /home/yashandb_odbc/libyas_odbc.so
   FileUsage       = 1
   ```

## Step 4: Configure Data Source

```shell
$ vi /etc/odbc.ini

[YASTEST]
Description  = YashanTest
Driver       = YashanDB
SERVER       = 127.0.0.1
PORT         = 1688
URL          = 127.0.0.1:1688
USER         = sys
PWD          = sys
```

The URL in the configuration is the complete URL of the data source. Please refer to the [C driver yacConnect function](../../C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect) for the url parameter. After configuring the URL parameter, the SERVER and PORT parameters will no longer take effect.

Charset settings can be made here (provided that the ANSI driver is used; the default ANSI ODBC charset in Linux is UTF8). The following data source configuration can be used to set the charset:

```shell
# Currently supported charsets include: ASCII, ISO88591, GBK, UTF8, UTF16, GB18030
CHARACTER_SET = UTF8
```

## Step 5: Test Connection

```shell
# Data source name as yastest for example
$ isql yastest -v

# View data source names
$ odbcinst -q -s

# View driver names
$ odbcinst -q -d

# View driver configuration
$ odbcinst -j
```

## Step 6: (Optional) Test Connection Using the Built-in Test Program of yasodbc

Execute yasodbctest, with the data source name as yastest for example.

```shell
$ ./yasodbctest yastest sys sys
# Or
$ ./yasodbctest yastest
```

Use exit to quit.

```shell
> exit
```

![](./image/yasodbctest.png)
