
The yex_server sandbox process is a daemon process that is spontaneously initiated by the yasdb process in specific scenarios. YashanDB independently loads relevant functionality modules into the sandbox process, utilizing process isolation concepts and interprocess communication technologies to enhance the security of related functionalities.

## Usage Scenarios

To improve security, YashanDB will automatically start the yex_server sandbox process to independently load the corresponding functionality modules when using the following functionalities:

- Calling external stored procedures (such as external UDFs).

- Performing INSERT, DELETE, UPDATE, and SELECT operations on remote tables via dblink.

The configuration parameters exposed in yex_server are described as follows:

**Groble Parameter**

- YDBC_BUFFER_SIZE

    The default memory pool size driven by the yex_server sandbox process is 64M, with a valid range of \[32M, 1T\]. If the value is adjusted to below the minimum of 32M, no error will be reported, but the actual effective value will still be the minimum. 

- CHARACTER_SET

    The default character set of the yex_server sandbox process is consistent with the instance character set, which is UTF8 by default. The configurable character can be ASCII,ISO88591,GBK,UTF8 or GB18030.

**Parameter for External Stored Procedures**

The current yex_server process does not require configuration of runtime parameters for external stored procedures.

**Parameter for dblink**

Please refer to [Syntax Definition of DBLINK](./Syntax Definition of DBLINK) for the details of the parameters related with DBLINK.

## Configuration Steps

This section illustrates the configuration steps using the adjustment of the yex_server process YDBC_BUFFER_SIZE as an example.

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

4. Stop yex_server. Once the application calling external stored procedures or opening the dblink, the yex_server will start automatically and the configuration will take effect.

    ```shell
    $ ps -efww | grep -w yex_server | grep -v grep  | awk '{print $2}' | xargs -x -i kill -9 {}
    ```
