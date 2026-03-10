## Parameter Initialization Configuration
<span id="paraminit" name="paraminit" class="yaslink"></span>
All system parameters of YashanDB have default values, allowing instances to start without any handling after product installation. The defaults are based on the minimal configuration principle for the database to run on personal PCs, which may not be suitable for production environments. Therefore, it is recommended to perform parameter initialization configuration when installing YashanDB.

The initialization of parameters is completed by configuring installation parameters during the product installation process.

The range and values of parameters that need to be initialized should be determined in conjunction with the company's own business and resource environment. Additionally, special attention should be paid to the performance-related parameters listed in [Database Configuration Tuning](../../Performance Tuning/Database Performance Fundamentals/Database Configuration Tuning).

If outdated parameters are used in the system, a runtime log warning will be produced during startup, but it will not affect the startup. You can determine whether the parameter has become outdated due to renaming and whether the outdated parameter needs to be adjusted to the corresponding new parameter based on the specific parameter descriptions and warning contents in [Configuration Parameters](../../Reference Manual/Configuration Parameters). If the new parameter is not configured, the configuration value of the outdated parameter will be automatically converted to the new parameter value.

```verilog
// When the configuration file is set with the outdated parameter BROADCAST_GTS_TIME, the run.log will print as follows:
2024-03-15 17:10:37.606 21615 [WARN] [PARAM] parameter BROADCAST_GTS_TIME is deprecated, it will obsolete in later version
```

## Parameter Adaptive Configuration

The database parameter configuration directly affects performance. The initialization and subsequent adjustment of parameter values rely on professional DBAs. To assist DBAs in making quick decisions and reducing operation and maintenance difficulties, YashanDB provides parameter adaptive functionality, which offers recommendations for tuning parameters based on environmental information and current load.

### Basic Principles

1. It can run during the NOMOUNT, MOUNT, or OPEN phases of the database, distinguishing between different table types: HEAP/TAC/LSC.

2. It obtains environmental information such as the number of CPU cores, total memory, and free memory.

3. Combined with user-specified CPU and memory limits, it calculates the available resources for the database.

4. Based on the available resources, it calculates memory-related parameters.

5. It tests the performance of the disks where data files and log files are located.

6. Based on the disk performance results, it calculates IO-related parameters.

### Operating Steps

1. Choose a time when no business is running and clean up other processes to avoid interference with resource calculations and testing.

2. Run the parameter configuration recommendation program as needed. For details, please refer to [DBMS_PARAM](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM).

    ```sql
    -- Generate recommended parameters using default parameters without writing to the configuration file.
    EXEC DBMS_PARAM.OPTIMIZE();

    -- Use the TAC table type, allocate 80% memory, 100% CPU, generate recommended parameters, and write to the configuration file.
    EXEC DBMS_PARAM.OPTIMIZE(True, 'TAC', 80);

    -- Use the default HEAP table type, allocate 100% memory, 100% CPU, generate recommended parameters, and do not write to the configuration file.
    EXEC DBMS_PARAM.OPTIMIZE(NULL, NULL, 100, 100);

    -- Use the LSC table type, allocate 100% memory, 100% CPU, specify the paths of datafile and redofile, generate recommended parameters and do not write to the configuration file.
    EXEC DBMS_PARAM.OPTIMIZE(NULL, 'LSC', NULL, NULL, '/home/yashan/data', '/home/yashan/redo');
    ```

3. View the latest recommended parameter information.

    ```sql
    SELECT DBMS_PARAM.SHOW_RECOMMEND() FROM dual;
    ```

4. Write the recommended parameter configuration to the database configuration file (this process will not take effect immediately).
    ```sql
    EXEC DBMS_PARAM.APPLY_RECOMMEND();
    ```

5. Restart the database to make the parameters effective.

## Modify Parameter Configuration

During system operation, manual adjustments may be needed for certain parameter values due to changes in applications, resource expansion, or performance tuning. At this time, you should evaluate whether adjusting the parameter will affect online business based on the descriptions of the corresponding parameters in [Configuration Parameters](../../Reference Manual/Configuration Parameters) and determine the appropriate timing for the operation.

### Modify Parameter Values

#### Statement Format

- [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER SYSTEM) statement: Modify system-level configuration parameters.
- [ALTER SESSION](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER SESSION) statement: Modify session-level configuration parameters, which by default only take effect in memory for the current session.

#### Parameter Description

parameter_name and parameter_value refer to the configuration parameter name and parameter value, respectively, and the parameter value must meet the system-specified value range. For details, please refer to [Configuration Parameters](../../Reference Manual/Configuration Parameters).

When executing ALTER SYSTEM, you can use the scope to set the effectiveness of the modified configuration parameters. The default is both.

*   spfile: Write the parameter value to the parameter file, which requires a restart to take effect.

*   memory: Write the parameter value to memory, effective immediately, but will be invalid after a restart.

*   both: Write the parameter value to both memory and the parameter file, effective immediately, and will also be effective after a restart.

> **Note**: 
>
> - ALTER SYSTEM or ALTER SESSION cannot be performed on readonly parameters; readonly parameters can only be configured during installation or use default values.
>
> - Generally, it is recommended to use SQL statements to modify configuration parameters. The modified parameter values will be persisted to the database configuration file. Directly editing the configuration file is not recommended.
>
> - In ISC Distributed Cluster Deployment, it is recommended to use the YCM graphical interface or the [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command line method for unified parameter configuration across multiple nodes. If you need to use SQL statements to modify configuration parameters, you should implement multi-node synchronization modification or modification for only this node according to the contents of type and node described in the development manual [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER SYSTEM).
>
> - For outdated parameters, the specific rules during configuration are as follows:
>   - Outdated parameters still support modification. A warning will be printed in the runtime log during modification, with contents similar to the example in [Parameter Initialization Configuration](#paraminit).
>   
>   - When modifying outdated parameters to take effect immediately and if they are deprecated due to renaming with no corresponding new parameters configured, the modified values of the outdated parameters will be automatically converted to the new parameter values.
>   
>   - For outdated parameters that take effect after a restart, the runtime log will also print warnings when restarting the database, with contents as above.

### View Configuration Parameters

After modification, you can check whether the configuration parameters are effective using the show command or by querying the V$PARAMETER view.

> **Note**: 
>
> Outdated parameters still support querying. The difference in the query results for outdated parameters is that the IS_DEPRECATED field is TRUE when querying the V$PARAMETER view.

```sql
show parameter param_name;
```

### Operation Example

***Example***

```sql
-- Modify the date format parameter
-- 1. View the current parameter value
SHOW PARAMETER date_format;
NAME              VALUE        
----------------- --------------
DATE_FORMAT       yyyy-mm-dd   
 
-- 2. Modify the parameter value; date_format requires a restart to take effect
ALTER SYSTEM SET date_format='yyyy-mm-dd hh24:mi:ss' scope=spfile;    
 
-- 3. Restart the database
-- 4. Log in and check the parameter value
SHOW PARAMETER date_format;
NAME            VALUE                  
--------------- ------------------------
DATE_FORMAT     yyyy-mm-dd hh24:mi:ss

SELECT name,value,default_value,is_deprecated FROM V$PARAMETER WHERE name = 'DATE_FORMAT';

NAME                                                             VALUE                                                            DEFAULT_VALUE                                                    IS_DEPRECATED 
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- ------------- 
DATE_FORMAT                                                      yyyy-mm-dd hh24:mi:ss                                            yyyy-mm-dd                                                       FALSE   

-- View outdated parameters
SELECT name,value,default_value,is_deprecated FROM V$PARAMETER WHERE IS_DEPRECATED = TRUE;

NAME                                                             VALUE                                                            DEFAULT_VALUE                                                    IS_DEPRECATED 
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- ------------- 
DIN_RECONNECT_TIME                                               5000                                                             5000                                                             TRUE         
CGROUP_FLAG                                                      0                                                                0                                                                TRUE         
BROADCAST_GTS_TIME                                               16                                                               5                                                                TRUE         

SELECT name,value,default_value,is_deprecated FROM V$PARAMETER WHERE name = 'BROADCAST_GTS_TIME';

NAME                                                             VALUE                                                            DEFAULT_VALUE                                                    IS_DEPRECATED 
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- ------------- 
BROADCAST_GTS_TIME                                               16                                                               5                                                                TRUE    
```
