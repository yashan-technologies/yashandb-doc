Connection listening is used to detect network attacks such as connection storms. YashanDB achieves this functionality by recording listening logs. For information on managing listening logs, please refer to [Listener Log Management](../../Database Administration/Log Management/Listener Log Management).

## Configuring Connection Listening

The connection listening functionality is controlled by the value of the LSNR_LOG parameter. After YashanDB is installed, the default setting is LSNR_LOG = ON, which enables the connection listening functionality.

When connection listening is enabled, all connection requests to YashanDB (including remote connections and UDS local connections, regardless of success or failure) will be recorded in the listening logs.

## Listening Logs

During the installation of YashanDB, the listening log file listener.log is automatically created. This file is located in the log/listener folder under the DATA directory.

The log records connection information such as SCN, user, connection status, IP, and port. This information can help DBAs quickly analyze the source of connections and respond promptly to malicious attacks, ensuring system security.

Below is an example of the content recorded in the listening log:

```verilog
2022-11-02 18:02:06.839 protocal = IPC  user = SYS status = SUCCESS
2022-11-02 18:50:34.438 protocal = IPC  user = SYS status = ERROR
2022-11-02 17:58:47.357 protocal = TCP  ip = 127.0.0.1 port = 60607 user = REGRESS status = SUCCESS
2022-11-02 17:59:59.414 protocal = TCP  ip = 127.0.0.1 port = 60693 user = REGRESS status = ERROR
```
