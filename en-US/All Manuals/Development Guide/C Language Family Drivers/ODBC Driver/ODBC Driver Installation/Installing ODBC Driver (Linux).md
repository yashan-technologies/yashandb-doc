This article uses Centos 7.3.1 as an example to introduce the installation and configuration process of the YashanDB ODBC driver in this environment.

## Step 1: Install unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## Step 2: Install YashanDB C Driver



### Download the C Driver Installation Package


1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



### Set Up Dynamic Library Dependency Path

1. Edit the bashrc file:

   ```shell
   $ vi ~/.bashrc
   ```

2. Add a new line to increase the LD_LIBRARY_PATH search path pointing to the folder where the C driver's library files are located:

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb-driver-c/lib
   ```

3. Save and exit.

4. Refresh the system variable configuration.

   ```shell
   $ source ~/.bashrc
   ```


## Step 3: Install YashanDB ODBC Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

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
URL          = 127.0.0.1:1688[/pdb_name]
USER         = sys
PWD          = sys
```

The URL in the configuration is the complete URL of the data source. After configuring the URL parameter, the SERVER and PORT parameters will no longer take effect. URL format is as follows:

* Single IP: `host:port[/pdb_name]`.

* Multiple IPs: `serverType:host:port,host:port,host:port,host:port[/pdb_name]`, multiple addresses are separated by `,`, connections will poll to corresponding nodes based on serverType configuration.

* Multiple IP groups: `serverType:host:port,host:port;host:port,host:port[/pdb_name]`, multiple IP groups are separated by `;`, when connecting, it will first poll the corresponding nodes within the group according to the serverType configuration. If all connections within the group fail, it will access the next group in order of priority (the higher the position, the higher the priority).

Parameter meanings:

* host:port[/pdb_name]: The listening address of the server. To connect to YashanDB deployed as a CDB, you must use the ODBC driver of version v23.5.1.100 or later and specify the correct [target container name](../../../../Database Administration/​​Multitenant Management/​​Managing PDBs​​/​View PDBs); if omitted, the connection will default to the CDB root.

* serverType: Connection mode, optional options include [primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance].

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
