## Configuring Connection Listening

The LSNR_LOG parameter controls the startup and shutdown of listener log recording, which is ON by default.

```sql
-- Check if the listener is currently active
SHOW PARAMETER LSNR_LOG;
NAME             VALUE     
---------------- ----------
LSNR_LOG         ON        

-- Disable the listener
ALTER SYSTEM SET LSNR_LOG= OFF SCOPE=SPFILE;

-- Enable the listener
ALTER SYSTEM SET LSNR_LOG= ON SCOPE=SPFILE;
```

When enabled, all connection requests to YashanDB (including remote TCP connections and UDS local connections, regardless of success or failure) will be recorded in the listener logs.

## Listener Log Management

During YashanDB installation, the listener log file listener.log is automatically created in the $YASDB_HOME/log/listener folder. When connection listening is enabled, all connection information is recorded in the listener.log file, including successful and failed connection records.

### Viewing Log Content

Log in to the server where the database is located using the installation user, and navigate to the storage path of the listener log files to view the corresponding file.

```shell
$ cd $YASDB_DATA/log/listener
$ ls -lrt
total 4
-rw-r----- 1 yashan yashan 2274 Mar 23 11:45 listener.log

$ cat listener.log
```

#### Remote Connection Related Listener Logs

YashanDB remote connections follow the TCP/IP protocol. The listener log will record the SCN, TCP protocol, IP, port number, username, and connection status information, as shown below:

```verilog
2022-11-02 17:58:47.357 protocal = TCP  ip = 127.0.0.1 port = 60607 user = SALES status = SUCCESS
2022-11-02 17:59:59.414 protocal = TCP  ip = 127.0.0.1 port = 60693 user = SALES status = ERROR
```

#### UDS Local Connection Related Listener Logs

YashanDB's UDS local connections follow the IPC protocol. The listener log will record the SCN, IPC protocol, username, and connection status information, as shown below:

```verilog
2022-11-02 18:02:06.839 protocal = IPC  user = SYS status = SUCCESS
2022-11-02 18:50:34.438 protocal = IPC  user = SYS status = ERROR
```

### Configuring the Automatic Cleanup Strategy

By default, there is no upper limit on the size of the listener log file. When the listener.log file becomes too large, it may occupy excessive disk space, resulting in waste. In such cases, manual editing and deletion of the physical file are required according to the actual situation.

To simplify operations and maintenance, YashanDB provides an automatic cleanup mechanism for listener logs. When the relevant parameter configurations meet business requirements, no human intervention is needed.

|Parameter Name |Parameter Description                           |Default Value |
| --------------------------- | ------------------------------------------------------------ | ------------------------ |
| LISTENER_LOG_ENABLED        | Whether to perform automatic cleanup of listener logs:<br />\- TRUE: Perform automatic cleanup.<br />\- FALSE: Do not perform automatic cleanup. | FALSE                    |
| LISTENER_LOG_FILE_SIZE      | This parameter takes effect only when LISTENER_LOG_ENABLED = TRUE.<br />The maximum size of a single listener log file. When the file size reaches this upper limit, it is automatically archived. The archived file name is `listener-YYYYMMDDHHMMSS.log`. | 20M                      |
| LISTENER_LOG_FILE_COUNT     | This parameter takes effect only when LISTENER_LOG_ENABLED = TRUE.<br />The total number of listener log files, that is, 1 listener.log file in use and N - 1 archived `listener-YYYYMMDDHHMMSS.log` files. <br />When the number of files reaches this upper limit, the earliest `listener-YYYYMMDDHHMMSS.log` file is automatically cleaned up. | 10                       |

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the relevant parameter configurations.

    ```sql
    SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'LISTENER_LOG_%';
    NAME                               VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    LISTENER_LOG_ENABLED                                             FALSE
    LISTENER_LOG_FILE_COUNT                                          10
    LISTENER_LOG_FILE_SIZE                                           20M
    ```

3. Configure the automatic cleanup strategy as needed.

    ```sql
    -- Enable automatic cleanup
    ALTER SYSTEM SET LISTENER_LOG_ENABLED=TRUE SCOPE=SPFILE;
    
    -- Adjust the capacity of a single listener log file
    ALTER SYSTEM SET LISTENER_LOG_FILE_SIZE=40M SCOPE=SPFILE;
    
    -- Adjust the total number of listener log files
    ALTER SYSTEM SET LISTENER_LOG_FILE_COUNT=15 SCOPE=SPFILE;
    ```
