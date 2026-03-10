YStream is a logical log parsing interface provided by YashanDB. Its server-side API (also referred to as the "YStream server") is managed by built-in functions provided by the DBMS_YSTREAM_ADM package, including creating, adjusting configurations, starting/stopping, and deleting.

> **Note**: 
>
> - No YStream functionality is available in ISC Distributed Cluster Deployment.
> - Users executing this package must have the YSTREAM_CAPTURE role.
> - This package cannot operate on configurations related to logical standby databases.

Prerequisites for using YStream servers are as follows:

- The STREAM_POOL_SIZE value must be configured reasonably as needed before creating the YStream server.

- Archiving mode must be enabled before creating the YStream server.

- It is recommended to enable supplemental logging ([database-level](../../SQL参考手册/SQL语句/ALTER DATABASE.html#supplementallogclauses) or [table-level](../../SQL参考手册/SQL语句/ALTER DATABASE.html#addsupplementalloggingclause)) before creating the YStream server. If not enabled, only supplemental information from existing logs can be parsed, and enabling supplemental logging after an existing YStream server will result in an error.

<span id="STATUS" name="STATUS" class="yaslink"></span>

The YStream server uses a status to indicate its operating condition, and the management operations allowed vary slightly depending on the status. Information about the status and type of the YStream server can be obtained by querying the [V$YSTREAM_SERVER](../../../Reference Manual/System Views/Dynamic Performance Views/V$YSTREAM_SERVER) view.

|Status |Description |Allowed Operations |
| :---- |:-----------|:-----------|
| CREATED | Initial state, indicates the YStream server is created but not started | * [ADD_TABLES](#add_tables): Add parsing table names and schemas<br/>* [DROP_TABLES](#drop_tables): Remove parsing table names and schemas<br/>* [SET_PARAMETER](#set_parameter): Set parameters<br/>* [START](#start): Start the YStream server<br/>* [DROP](#drop): Delete the YStream server |
| STARTED | Indicates the YStream server has been started, waiting for the [YStream client](../../YStream参考手册/00YStream参考手册) to connect | The instance that starts the YStream server allows:<br/>* [ADD_TABLES](#add_tables): Add parsing table names and schemas<br/>* [STOP](#stop): Stop the YStream server<br/><br/> When the instance that starts the YStream server is offline, other instances in the cluster allow:<br/>* [STOP](#stop): Stop the YStream server<br/>* [TEST](#test): Test if the start_scn of the YStream server is appropriate |
| RUNNING | * Indicates that the [YStream client](../../YStream参考手册/00YStream参考手册) has connected, and the YStream server is running on the current instance<br/>* Indicates that the current instance is executing the [TEST](#test) function | The instance that starts the YStream server allows:<br/>* [ADD_TABLES](#add_tables): Add parsing table names and schemas<br/>* [STOP](#stop): Stop the YStream server<br/><br/> When the instance that starts the YStream server is offline, other instances in the cluster allow:<br/>* [STOP](#stop): Stop the YStream server |
| STOPPED | Indicates the YStream server has stopped | * [ADD_TABLES](#add_tables): Add parsing table names and schemas<br/>* [DROP_TABLES](#drop_tables): Remove parsing table names and schemas<br/>* [SET_PARAMETER](#set_parameter): Set parameters<br/>* [START](#start): Start the YStream server<br/>* [DROP](#drop): Delete the YStream server |

<span id="create" name="create" class="yaslink"></span>

## CREATE

```plsql
DBMS_YSTREAM_ADM.CREATE(
    server_name    IN  VARCHAR(64), 
    connect_user   IN  VARCHAR(64) DEFAULT NULL, 
    start_scn      IN  BIGINT DEFAULT NULL
);
```

The CREATE function is used to create a YStream server.

When the YStream server is created, a log parsing starting point is recorded. If the database has archiving enabled, all unparsed archived logs after the log parsing starting point will not be automatically cleaned regardless of the YStream server's state. If it is no longer necessary to use the logs parsed by a certain YStream server, please delete any unnecessary YStream servers promptly to avoid hindering the database's automatic cleanup of archives, leading to wasted storage space.

Function usage rules are as follows:

- Maximum number of YStream servers:
  - In Standalone Deployment, a maximum of 32 YStream servers can be created per instance.
  - In YAC/Distributed Cluster Deployment, a maximum of 32 YStream servers can be created per cluster, and the YStream servers can run on any instance in the cluster.
- In a high-availability environment, this function can only be called on the primary database / primary cluster.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server, must not conflict with existing server names; this parameter cannot be NULL               |
| connect_user | The database user allowed to connect to the YStream server; the specified user must have YSTREAM_CAPTURE privilege; NULL indicates all database users are allowed to connect<br />Due to the impact of YashanDB's syntax mode on the case-sensitivity rule of usernames, please escape usernames according to actual requirements:<br/>- yashan mode: Case-insensitive and automatically converted to uppercase. If case-sensitive username handling is required, enclose the username in double quotes—for example, for username `User`, specify `'"User"'`<br/>- mysql mode: Case-sensitive, and no automatic conversion is performed. If double quotes are added, they will be treated as part of the username—for example, `'"User"'` will be parsed as `"User"` |
| start_scn    | The starting point for log parsing; NULL indicates starting from the current position.<br/><br/>If start_scn is within a DDL transaction, only logs after that DDL transaction can be parsed, as YStream cannot build metadata from incomplete DDL parsing. All subsequent logs related to that DDL will be ignored. In this scenario, it is necessary to recreate the YStream server with a new start_scn to avoid it being within a DDL transaction.<br/><br/>You can call the [TEST](#test) function to test if start_scn is appropriate after creating and starting the YStream server.<br/><br/>YStream only supports parsing redo or archive log files generated under archiving mode; ensure that logs after start_scn meet the criteria. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.CREATE('server_1');
EXEC DBMS_YSTREAM_ADM.CREATE('server_2', 'sales');
EXEC DBMS_YSTREAM_ADM.CREATE('server_3', 'sales', 563517721634004992);
EXEC DBMS_YSTREAM_ADM.CREATE('server_4');
EXEC DBMS_YSTREAM_ADM.CREATE('server_5');
```

<span id="add_tables" name="add_tables" class="yaslink"></span>

## ADD\_TABLES

```plsql
DBMS_YSTREAM_ADM.ADD_TABLES(
    server_name   IN  VARCHAR(64),
    table_names   IN  VARCHAR(4096),
    schemas       IN  VARCHAR(4096)
);
```

The ADD_TABLES function is used to add parsing table names and schemas to an existing YStream server.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- In high availability environments, this function can only be called on the primary database / primary cluster.
- If ADD_TABLES is not executed or if both table_names and schemas parameters are set to empty strings, it indicates no filtering will be done, and all tables and schemas will be parsed.
- The system does not validate if the provided table names and schema names exist in the table_names and schemas parameters, facilitating the configuration (preliminary addition of planned but not yet created tables or schemas to the YStream server).
- It is allowed to input duplicate table names and schema names in the table_names and schemas parameters, and the system will automatically deduplicate them without error.
- It is allowed to add parsing table names and schemas to the same YStream server multiple times, and the final result will be the sum of multiple operations (the automatically deduplicated union).

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                               |
| table_names  | List of table names to add; if table names or schema names contain spaces or special characters, use double quotes `""` to enclose them, separated by commas; each server supports up to 1,000,000 tables. When specifying table names, you can also specify the schema where the table is located, e.g., 'sales.tab'; the default schema for tables is the current schema. If no table names are specified, please pass NULL. |
| schemas      | List of schemas to add; if schema names contain spaces or special characters, use double quotes `""` to enclose them, separated by commas; each YStream server supports up to 10,000 schemas. If no schemas are specified, please pass NULL. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_1', 'sales.employees', '');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_2', 'sales.employees,sales.employees,sales.department,sales.branches', '');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_3', '', 'schema1,schema2');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_4', 'sales.department,sales.branches', '"schema{3}",schema4');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_5', '', '');
```

<span id="drop_tables" name="drop_tables" class="yaslink"></span>

## DROP\_TABLES

```plsql
DBMS_YSTREAM_ADM.DROP_TABLES(
    server_name   IN  VARCHAR(64), 
    table_names   IN  VARCHAR(4096),
    schemas       IN  VARCHAR(4096)
);
```

The DROP_TABLES function is used to remove table names and schemas from the YStream server.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- In high availability environments, this function can only be called on the primary database / primary cluster.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                               |
| table_names  | List of table names to delete; if table names or schema names contain spaces or special characters, use double quotes `""` to enclose them, separated by commas. When specifying table names, you can also specify the schema where the table is located, e.g., 'sales.tab'; the default schema for tables is the current schema. If no table names are specified, please pass NULL. |
| schemas      | List of schemas to delete; if schema names contain spaces or special characters, use double quotes `""` to enclose them, separated by commas. If no schemas are specified, please pass NULL. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.DROP_TABLES('server_1', 'sales.department,sales.branches', '');
EXEC DBMS_YSTREAM_ADM.DROP_TABLES('server_2', 'sales.department,sales.department', 'schema1,schema2');
```

<span id="set_parameter" name="set_parameter" class="yaslink"></span>

## SET\_PARAMETER

```plsql
DBMS_YSTREAM_ADM.SET_PARAMETER(
    server_name   IN  VARCHAR(64), 
    parameter     IN  VARCHAR(64), 
    value         IN  VARCHAR(64)
);
```

The SET_PARAMETER function is used to set parameters for an existing YStream server.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- In high availability environments, this function can only be called on the primary database / primary cluster.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                               |
| parameter    | The parameter name; this parameter cannot be NULL                                                                             |
| value        | The parameter value; it should be within an acceptable range; this parameter cannot be NULL                                   |

The following parameters can be set:

|Parameter Name |Value Range |Default Value |Description |
| :---- |:-----------|:-----------|:-----------|
| PARALLELISM                        | [1,128]     | 1             | The concurrency of redo parsing threads; specifies the number of concurrent threads for parsing logs of one instance in one YStream server.<br/><br/>* In YAC/Distributed Cluster Deployment, YStream servers must parse logs of all instances concurrently; in a cluster with M instances, starting a YStream server with parallelism N will create `M*N` log parsing threads.<br/>* In Standalone Deployment, the YStream server only needs to parse logs from the current instance, creating `1*N` log parsing threads with parallelism N.<br/><br/>Setting a high concurrency might consume more resources and trigger operating system thread limits; please configure or adjust concurrency responsibly. |
| TXN_AGE_SPILL_THRESHOLD             | [1,100000]  | 600           | The time threshold (in seconds) for triggering LCR overflow. During parsing, if a transaction is not committed for a long time and exceeds this value, all LCRs of that transaction will overflow to the system table for persistence, releasing memory. The logs corresponding to such LCRs do not need to be re-parsed; users can clean the archive log files as needed. |
| TXN_LCR_SPILL_THRESHOLD             | [32K,1T]    | 128M          | The memory usage threshold (in bytes) for triggering LCR overflow. During parsing, if the memory occupied by a transaction exceeds this value, all LCRs of that transaction will overflow to the system table for persistence, releasing memory. The logs corresponding to such LCRs do not need to be re-parsed; users can clean the archive log files as needed. |
| CHECKPOINT_INTERVAL                 | [1,3600]    | 3             | The interval (in seconds) at which Checkpoints are executed. Each Checkpoint persists the latest recovery point in the system table; after the client sets the latest applied position, it needs to wait for a Checkpoint interval to be recognized by the database. |

Example (Standalone, YAC Deployment)

 ```plsql
 EXEC DBMS_YSTREAM_ADM.SET_PARAMETER('server_1', 'PARALLELISM', '3');
 ```

<span id="start" name="start" class="yaslink"></span>

## START

```plsql
DBMS_YSTREAM_ADM.START(
    server_name   IN  VARCHAR(64)
);
```

The START function is used to start an existing YStream server. Only after the YStream server has been started can the [YStream client](../../YStream参考手册/00YStream参考手册) establish a connection.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- Each YStream server allows clients to connect only to the instance that started that server. If a connection switch is required during subsequent usage, the YStream server must first be [stopped](#stop), and then it can be restarted on the target instance.
- If the `start_scn` of the target YStream server is too old (the UNDO log corresponding to the SCN has been cleared), a "too old snapshot" prompt will appear during startup. In case of such a situation, it is recommended to first delete the target YStream server, then recreate a YStream server with the same name and specify a new `start_scn`.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.START('server_1');
EXEC DBMS_YSTREAM_ADM.START('server_2');
```

<span id="test" name="test" class="yaslink"></span>

## TEST

```plsql
DBMS_YSTREAM_ADM.TEST(
    server_name   IN  VARCHAR(64),
    target_scn    IN  BIGINT
);
```

The TEST function is used to test if the start_scn of an existing YStream server is appropriate; the test does not generate logical logs, and the client does not need to connect to the YStream server.

This function scans from the starting point of the YStream server, start_scn, through redo files until the user-defined target target_scn, checking if start_scn is in the middle of a DDL transaction.

If it is found during testing that a DDL commit log exists, but the begin scn of that DDL is before the current YStream server's start_scn, it cannot be fully parsed, meaning that the corresponding metadata cannot be correctly maintained, and parsing cannot continue. In this case, it is necessary to recreate the YStream server and specify a suitable start_scn to avoid being in the middle of a DDL transaction.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- Each YStream server allows clients to connect only to the instance that started that server. If a connection switch is required during subsequent usage, the YStream server must first be [stopped](#stop), and then it can be restarted on the target instance.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                |
| target_scn   | The SCN corresponding to the endpoint of the test, which can be specified as the latest SCN of the current database; this parameter cannot be NULL. If the target_scn is less than the YStream server's start_scn, the system automatically adjusts it to start_scn for fault tolerance. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.TEST('server_1', 563517721634005992);
```

<span id="stop" name="stop" class="yaslink"></span>

## STOP

```plsql
DBMS_YSTREAM_ADM.STOP(
    server_name   IN  VARCHAR(64),
    force         IN  BOOL DEFAULT FALSE
);
```

The STOP function is used to stop an existing YStream server.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- In YAC/Distributed Cluster Deployment, the start and stop of the same YStream server are usually required to be operated on the same instance, unless that instance is unavailable (for example, offline), then stopping the YStream server on another instance is allowed. If a primary/standby cluster deployment is used, it is always disallowed to stop a YStream server started on the primary cluster from a standby cluster instance.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                |
| force        | Force stop the YStream server. When connected to a standby database and the connection between standby databases is disconnected, ignore errors and directly stop the YStream server; the state in the system table will not change. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.STOP('server_1');
EXEC DBMS_YSTREAM_ADM.STOP('server_2', TRUE);
```

<span id="drop" name="drop" class="yaslink"></span>

## DROP

```plsql
DBMS_YSTREAM_ADM.DROP(
    server_name   IN  VARCHAR(64)
);
```

The DROP function is used to delete an existing YStream server.

Function usage rules are as follows:

- For the status requirements of the target YStream server for this operation, please refer to [Status Introduction](#STATUS).
- In high availability environments, this function can only be called on the primary database / primary cluster.

|Parameter |Description |
| :---- |:-----------|
| server_name  | The name of the YStream server; this parameter cannot be NULL                                                |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_YSTREAM_ADM.DROP('server_1');
```
