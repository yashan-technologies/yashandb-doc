YashanDB records listener logs by default. Whether to record is controlled by the `LSNR_LOG` parameter. The default is `LSNR_LOG = ON`. It is not recommended to disable listener logging unless necessary. If the feature is currently disabled, you can refer to [Managing Listener Log Configuration](../../../Product Security/Connection Management/Managing Connection Listener.md#LSNR_LOG) to enable it as needed.

When enabled, all connection requests to YashanDB (including remote TCP connections and UDS local connections, regardless of success or failure) will be recorded in the listener logs.

## Viewing Log Content

The listener log file `listener.log` is stored in the `$YASDB_DATA/log/listener` folder. Log in to the server where the database is located using the installation user to view the log file.

```shell
$ cd $YASDB_DATA/log/listener
$ ls -lrt
total 4
-rw-r----- 1 yashan yashan 2274 Mar 23 11:45 listener.log

$ cat listener.log
```

### Remote Connection Related Listener Logs

YashanDB remote connections follow the TCP/IP protocol. The listener log will record the SCN, TCP protocol, IP, port number, username, and connection status information, as shown below:

```verilog
2022-11-02 17:58:47.357 protocal = TCP  ip = 127.0.0.1 port = 60607 user = SALES status = SUCCESS
2022-11-02 17:59:59.414 protocal = TCP  ip = 127.0.0.1 port = 60693 user = SALES status = ERROR
```

### UDS Local Connection Related Listener Logs

YashanDB's UDS local connections follow the IPC protocol. The listener log will record the SCN, IPC protocol, username, and connection status information, as shown below:

```verilog
2022-11-02 18:02:06.839 protocal = IPC  user = SYS status = SUCCESS
2022-11-02 18:50:34.438 protocal = IPC  user = SYS status = ERROR
```

## Log Cleanup and Archiving

The `listener.log` file has no size limit. When the file is too large, it may occupy excessive disk capacity and cause waste. It is necessary to manually edit and delete the physical file or archive it as needed based on the actual situation.