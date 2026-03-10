The yex_server sandbox process is a daemon process that is spontaneously initiated by the yasdb process in specific scenarios. YashanDB independently loads relevant functionality modules into the sandbox process, utilizing process isolation concepts and interprocess communication technologies to enhance the security of related functionalities.

## Usage Scenarios

To improve security, YashanDB will automatically start the yex_server sandbox process to independently load the corresponding functionality modules when using the following functionalities:

- Calling external stored procedures (such as external UDFs).

- Performing INSERT, DELETE, UPDATE, and SELECT operations on remote tables via dblink.

## Prerequisites

You need to [install the libaio library and download the Oracle Instant Client installation package](../../数据库管理/基本数据库管理/异构数据库链接配置).

## Adjusting Memory Pool Size

The default memory pool size driven by the yex_server sandbox process is 64M, with a valid range of \[32M, 1T\]. If the value is adjusted to below the minimum of 32M, no error will be reported, but the actual effective value will still be the minimum. This configuration is controlled by the YDBC_BUFFER_SIZE parameter in the yex_server.ini configuration file.

After installing YashanDB, the yex_server.ini file will not be generated automatically. You can create this file based on actual needs and adjust this parameter value.

1. Check if the yex_server.ini file exists in the $YASDB_DATA/external/server path. If it does not exist, create it:

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # This document uses /data/yashan/yasdb_data/db-1-1 as an example

    $ cd /data/yashan/yasdb_data/db-1-1/external/server
    $ ll
    $ vi yex_server.ini
    ```
    
2. Add or modify the following configuration in the yex_server.ini file:

    ```shell
    YDBC_BUFFER_SIZE = 64M
    ```

3. Save and exit the editor.

4. The configuration will take effect after restarting the instance.

    ```shell
    $ yasboot cluster restart -c yashandb
    ```
