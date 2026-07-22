The mysql mode is primarily designed for User Managerment, Objects Managerment and Data Read/Write; when performing ​​database administration tasks, please switch the session to yashan mode.

## Database Management Operations that Require Switching to yashan Mode

-  Backup and restoration
-  Flashback
-  Instance startup and shutdown
-  Primary-standby switchover
-  Data synchronization
-  Log management
-  File management
-  Tablespace and tablespace set management
-  Table management
-  Resource management
-  Fault diagnosis
-  Session and scheduling management

> **Note**:
>
> Backup and recovery in mysql mode does not have backup set encryption functionality.

## Operation Steps

1. Connect using YashanDB's native client and tools. Here we use the yasql as an example, with the default port being 1688.

    ```shell
    $ yasql username/password@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

    SQL> 
    ```

2. Switch the syntax mode of the current session.

    ```sql
    -- check the syntax mode of the current session
    SHOW PARAMETER COMPAT_VECTOR;

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    mysql    

    -- switch to yashan mode
    ALTER SESSION SET COMPAT_VECTOR = yashan;

    SHOW PARAMETER COMPAT_VECTOR;

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    yashan    
    ```

3. After switching to the yashan mode, you can refer to the Database Management section to learn detailed database management steps. After completing the operations, close the session or switch the session back to mysql mode before proceeding with business data management operations.

    ```sql
    -- switch to mysql mode
    ALTER SESSION SET COMPAT_VECTOR = mysql;

    SHOW PARAMETER COMPAT_VECTOR

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    mysql    
    ```