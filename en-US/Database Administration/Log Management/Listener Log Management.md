## Listener Log Startup and Shutdown

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

This parameter requires a database restart to take effect.

## Log Archiving

There is no upper limit set for the size of listener log files. When the listener.log file becomes too large, you need to manually clean up the listener logs.

## Listener Entries

After the listener is started, all connection information will be recorded in the listener.log file, including records of successful and failed connections.

### **Remote Connection Listener**

YashanDB remote connections follow the TCP/IP protocol. The listener log will record the SCN, TCP protocol, IP, port number, username, and connection status information, as shown below:

```verilog
2022-11-02 17:58:47.357 protocal = TCP  ip = 127.0.0.1 port = 60607 user = SALES status = SUCCESS
2022-11-02 17:59:59.414 protocal = TCP  ip = 127.0.0.1 port = 60693 user = SALES status = ERROR
```

### **UDS Local Connection Listener**

YashanDB's UDS local connections follow the IPC protocol. The listener log will record the SCN, IPC protocol, username, and connection status information, as shown below:

```verilog
2022-11-02 18:02:06.839 protocal = IPC  user = SYS status = SUCCESS
2022-11-02 18:50:34.438 protocal = IPC  user = SYS status = ERROR
```
