This article uses Centos 7.3.1 as an example to introduce the installation and configuration process of the YashanDB ODBC driver in this environment.

## Step 1: Install unixODBC

```shell
yum install unixODBC-devel
yum install unixODBC
```

## Step 2: Install YashanDB C Driver

Using the YashanDB ODBC driver requires the YashanDB C driver library to be installed and configured first. The installation files for the YashanDB C driver are integrated into the YashanDB client installation package, and the corresponding client installation package must be obtained during installation.



### Step 2. 1: Download the C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * bin: Executable files for the C driver (currently includes *yasql*).

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.

### Step 2. 2: Set Up Dynamic Library Dependency Path



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
CHARACTER_SET = UTF8   # The default character set of the ODBC driver is UTF8. To use other character sets, configure this parameter as needed. Available options include: ASCII, ISO88591, GBK, UTF8, GB18030
```

The URL in the configuration is the complete URL of the data source. After configuring the URL parameter, the SERVER and PORT parameters will no longer take effect. 

URL format is as follows:

* Single address connection: `host:port`.

* Multiple addresses connection: `serverType:host:port,host:port,host:port,host:port`, multiple addresses are separated by `,`, and the connection is made to the corresponding node based on the serverType configuration during connection.

* Multiple address groups connection: `serverType:host:port,host:port;host:port,host:port`, multiple groups of addresses are separated by `;` and multiple addresses within the same group are separated by `,`. During connection, the connection is first made to the corresponding node within the group based on the serverType configuration. When all connections within the group fail, the next group is accessed in order of priority (the earlier, the higher priority).

Parameter meanings:

* host:port：

    * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address.

    * port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688. 

* serverType: The connection type for multi-address connection. Optional values include primary, standby, loadBalance, primaryLoadBalance, and standbyLoadBalance. If serverType is not specified, primary is used by default when multiple IPs are entered. The detailed introduction of each type is as follows:

    |serverType |Description |
    |--------------------|---------------|
    | primary | This is the default type and can be omitted.<br />The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time. |
    | standby | The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the standby node for the first time.  |
    | loadBalance | The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | primaryLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among primary nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | standbyLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among standby nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |





>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.




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
