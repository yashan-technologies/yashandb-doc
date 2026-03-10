YashanDB processes connection requests from clients through a working thread within the database instance, which is called the connection listener.



The connection listener operates in the following modes:

- Normal operation mode: Accepts new connections, defaults to this mode, which can be adjusted through ALTER SYSTEM statement.

- Maintenance mode: Maintains existing connections and blocks new connections (except local connections initiated by sys). After the listener enters maintenance mode, it will only allow local connection sessions initiated by sys users to connect to the database, until it is adjusted back to normal operation mode or the database is restarted.



DBA can configure the connection listener's operating mode based on current connection load, connection admission principles, and other considerations as needed, thereby controlling whether to accept new connections and sessions without shutting down the database.

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute the [ALTER SYSTEM](../../开发手册/SQL参考手册/SQL语句/ALTER SYSTEM) statement to adjust the connection listener's operating mode. The configuration takes effect immediately.

    ```sql
    -- Sets the connection listener to maintenance mode
    ALTER SYSTEM LISTENER STOP;

    -- Sets the connection listener to normal operation mode
    ALTER SYSTEM LISTENER START;
    ```

    After setting the connection listener to maintenance mode, only local connections initiated by sys can be used normally, while other scenarios will prompt `listener closed`.
