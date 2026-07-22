## Slow Log Activation and Deactivation

Control the activation and deactivation of slow log recording by setting the ENABLE_SLOW_LOG parameter, which is disabled by default.

Slow logs are not applicable to ISC Distributed Cluster Deployment.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check if the slow log is currently enabled
SHOW PARAMETER ENABLE_SLOW_LOG;
NAME                VALUE     
------------------- ----------
ENABLE_SLOW_LOG     FALSE        

-- Disable the slow log
ALTER SYSTEM SET ENABLE_SLOW_LOG = FALSE;

-- Enable the slow log
ALTER SYSTEM SET ENABLE_SLOW_LOG = TRUE;
```

## Set Slow Log Time Threshold

Control the time threshold in the slow log by setting the SLOW_LOG_TIME_THRESHOLD parameter, with the unit in milliseconds. SQL executions that exceed the time threshold will be considered slow SQL and recorded in the slow log.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current slow log time threshold
SHOW PARAMETER SLOW_LOG_TIME_THRESHOLD
NAME                        VALUE     
--------------------------- ----------
SLOW_LOG_TIME_THRESHOLD     1000        

-- Set the time threshold
ALTER SYSTEM SET SLOW_LOG_TIME_THRESHOLD = 2000;
```

## Set Maximum Length of SQL Records in Slow Logs

Control the maximum length of SQL statements recorded in the slow log by setting the SLOW_LOG_SQL_MAX_LEN parameter, with the unit in bytes. SQL statements exceeding this value will be truncated in the record.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current maximum length of SQL records in the slow log
SHOW PARAMETER SLOW_LOG_SQL_MAX_LEN
NAME                        VALUE     
--------------------------- ----------
SLOW_LOG_SQL_MAX_LEN        2000        

-- Set the maximum length of SQL records in the slow log
ALTER SYSTEM SET SLOW_LOG_SQL_MAX_LEN = 1000;

-- Set the SQL records in the slow log to have no length limit
ALTER SYSTEM SET SLOW_LOG_SQL_MAX_LEN = 0;
```

## Set Maximum Length of Individual Bind Parameters in Slow Logs

Control the maximum length of individual bind parameters recorded in the slow log by setting the SLOW_LOG_PARAM_MAX_LEN parameter, with the unit in bytes. Bind parameters exceeding this value will be truncated in the record.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current maximum length of individual bind parameters in the slow log
SHOW PARAMETER SLOW_LOG_PARAM_MAX_LEN
NAME                        VALUE     
--------------------------- ----------
SLOW_LOG_SQL_MAX_LEN        2000        

-- Set the maximum length of individual bind parameters in the slow log
ALTER SYSTEM SET SLOW_LOG_PARAM_MAX_LEN = 1000;
```

## Set Slow Log File Name

Control the name of the slow log file by setting the SLOW_LOG_FILE_NAME parameter.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current slow log file name
SHOW PARAMETER SLOW_LOG_FILE_NAME
NAME                      VALUE     
------------------------- ----------
SLOW_LOG_FILE_NAME        slow.log

-- Set the slow log file name
ALTER SYSTEM SET SLOW_LOG_FILE_NAME = myslow.log SCOPE = SPFILE;
```

## Set Storage Path for Slow Log Files

Control the storage path of the slow log file by setting the SLOW_LOG_FILE_PATH parameter.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current storage path of the slow log file
SHOW PARAMETER SLOW_LOG_FILE_PATH
NAME                    VALUE     
----------------------- ------------
SLOW_LOG_FILE_PATH      ?/slow/log

-- Set the storage path of the slow log file
ALTER SYSTEM SET SLOW_LOG_FILE_PATH = '/home/user/log/slow/' SCOPE = SPFILE;
```

## Set Output Method for Slow Log Records

Control the output method for slow log records by setting the SLOW_LOG_OUTPUT parameter, which currently supports TABLE and FILE types:

- TABLE: Slow logs output to the SLOW_LOG$ system table.

- FILE: Slow logs output to the slow log file.

In high availability deployments, the output method for the standby database's slow logs is not affected by this parameter and will always output to the slow log file.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Check the current output method for slow log records
SHOW PARAMETER SLOW_LOG_OUTPUT
NAME                      VALUE     
----------------       ------------
SLOW_LOG_OUTPUT           FILE

-- Set the output method for slow log records
ALTER SYSTEM SET SLOW_LOG_OUTPUT = 'FILE';
```

## Log Archiving

There is no upper limit set for the size of the slow log recording files. When the slow.log file or the SLOW_LOG$ table becomes too large, manual cleanup is required.

> **Note**: 
>
> - Slow logs use an asynchronous output method, meaning that they will not be output immediately after execution but will be placed in a log queue, output in batches through background threads.
>
> - The maximum length of the log queue is 256, and threads output 64 records every 200ms.
>
> - Based on the above principles, logs may be lost in the event of a power outage; setting the time threshold too low may also lead to log loss if the queue is full.
>
> - In primary/standby environments, when the standby database sets SLOW_LOG_OUTPUT to FILE, slow logs will be output to files. Setting SLOW_LOG_OUTPUT to TABLE on the standby database will not cause an error but will not take effect, and logs will not continue to output to files. The content of SLOW_LOG$ in the standby database is directly synchronized with SLOW_LOG$ in the primary database.
>
> - According to the previous rule, it is not recommended to set SLOW_LOG_OUTPUT to TABLE on the standby database; if a TABLE mode primary database is switched to a standby database, note that its slow logs will not be output and you need to change the SLOW_LOG_OUTPUT parameter to FILE to enable the slow logs to be output to files.