YashanDB processes connection requests from clients through a working thread within the database instance, which is called the connection listener.

##  Managing Listener Operating Mode



The connection listener operates in the following modes:

- Normal operation mode: Accepts new connections, defaults to this mode, which can be adjusted through ALTER SYSTEM statement.

- Maintenance mode: Maintains existing connections and blocks new connections (except local connections). After the listener enters maintenance mode, it will only allow local connection sessions to connect to the database, until it is adjusted back to normal operation mode or the database is restarted.



###  Applicable Scenarios

- Database maintenance: During database maintenance operations, setting to maintenance mode can reject new remote connections to ensure maintenance operations are not interfered with by new connections.
- Emergency circuit breaking: When connection storms or other abnormal situations occur, quickly stop accepting new connections to prevent database overload.
- Version upgrade: In non-stop upgrade scenarios, you can first set to maintenance mode and perform the upgrade after all sessions end.

###  Procedure

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute the [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM) statement to adjust the connection listener's operating mode. The configuration takes effect immediately.

    ```sql
    -- Sets the connection listener to maintenance mode
    ALTER SYSTEM LISTENER STOP;

    -- Sets the connection listener to normal operation mode
    ALTER SYSTEM LISTENER START;
    ```

    After setting the connection listener to maintenance mode, only local connections can be used normally, while other scenarios will prompt `listener closed`.

<span id="LSNR_LOG" name="LSNR_LOG"></span>

##  Managing Listener Log Configuration

In YashanDB, whether to record listener logs is controlled by the `LSNR_LOG` parameter. The default is `LSNR_LOG = ON` (i.e., logs are recorded). Therefore, when installing YashanDB, the listener log file $YASDB_DATA/log/listener/listener.log is automatically created. All connection requests to YashanDB (including remote TCP connections and UDS local connections, regardless of success or failure) will be recorded in the listener logs.

The log content includes timestamp, protocol type, IP address, port number, username, and connection status. For detailed log viewing methods and content examples, see [Listener Log Management](../../Database Administration/Operation Monitoring/Log Management/Listener Log Management).

###  Applicable Scenarios

- Security audit: Records all connection requests to the database to meet security compliance requirements.
- Attack detection: Detects network attack behaviors such as connection storms to promptly discover abnormal connections.
- Problem diagnosis: When client connections are abnormal, locate the cause through log analysis.

> **Note**:
>
> It is not recommended to disable listener logging unless necessary. Only consider disabling it when logging has a significant impact on performance and the security risk is controllable.

###  Procedure

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute the [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM) statement to adjust the LSNR_LOG parameter value.

    ```sql
    --View current listener log configuration
    SHOW PARAMETER LSNR_LOG;
    NAME             VALUE
    ---------------- ----------
    LSNR_LOG         OFF

    -- OFF indicates listener log is not recorded, can be enabled as needed
    ALTER SYSTEM SET LSNR_LOG= ON SCOPE=SPFILE;

    -- If listener logging is no longer needed, can be disabled as needed
    ALTER SYSTEM SET LSNR_LOG= OFF SCOPE=SPFILE;
    ```

3. Restart the database for the configuration to take effect.
