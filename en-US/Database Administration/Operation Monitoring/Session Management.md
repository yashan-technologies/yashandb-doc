When the client or driver interface connects to the YashanDB database server, the connection listener will create or assign a corresponding thread for each client connection to handle that client connection session.

## Session Mode

For an introduction to session mode principles, please refer to [Session Mode Concept Introduction](../../Product Concepts/Instance Architecture/Application and Network).

By default, YashanDB uses shared thread session mode, where the REACTOR dispatch threads in the thread pool select idle threads from the thread pool to assign to waiting connections for access requests. The number of REACTOR dispatch threads is controlled by the configuration parameter MAX_REACTOR_CHANNELS. The default value is 1, which means the system configures 1 REACTOR dispatch thread by default to allocate worker threads for access requests.

In standalone and YAC/Distributed Cluster Deployment support switching to exclusive thread session mode; ISC Distributed Cluster Deployment must use shared cluster session mode, and shared thread session mode parameters must be configured through CN nodes.

## Modify Session Management Parameters

### Switching to Exclusive Thread Session Mode

When configuring exclusive thread session mode, simply configure the system parameters to meet any of the following conditions:

- MAX_REACTOR_CHANNELS = 0, indicating that the thread pool is not used, and an independent thread is allocated for each connection. For detailed configuration parameter descriptions, please refer to [MAX_REACTOR_CHANNELS System Parameter](../../Reference Manual/Configuration Parameters.md#maxreactorchannels).

- MAX_WORKERS >= MAX_SESSIONS, when the maximum threads in the thread pool exceed the maximum sessions allowed by the system, it indicates that each connection can obtain an exclusive worker thread. For detailed configuration parameter descriptions, please refer to [MAX_WORKERS System Parameter](../../Reference Manual/Configuration Parameters.md#maxworkers) and [MAX_SESSIONS System Parameter](../../Reference Manual/Configuration Parameters.md#maxsessions).

### Shared Thread Session Mode Optimization

When using shared thread session mode, session-related configurations/behaviors are also affected by the following parameters. Please configure the related parameter values reasonably according to actual needs:

- **[MAX_REACTOR_CHANNELS](../../Reference Manual/Configuration Parameters.md#max_reactor_channels)**: Configure the number of REACTOR threads in the thread pool. REACTOR threads are responsible for selecting idle threads from the thread pool to assign to waiting connections. The configuration range for this parameter is [0,16]. The larger the MAX_WORKERS and the more concurrent connections, the larger the recommended value for MAX_REACTOR_CHANNELS. Please tune the actual configuration parameters according to business needs.

- **[MAX_WORKERS](../../Reference Manual/Configuration Parameters.md#max_workers)**: The maximum number of threads in the shared thread pool, which is the maximum number of concurrent tasks in the database.
    
    Different behaviors and configuration suggestions/requirements of this parameter value:

    - When MAX_WORKERS is set to 0, the actual effective value of MAX_WORKERS is calculated as CPU count * 2.

    - In standalone and YAC/Distributed Cluster Deployment, when MAX_WORKERS >= MAX_SESSIONS, the thread pool is closed (even if MAX_REACTOR_CHANNELS > 0, it will not take effect).

    - In ISC Distributed Cluster Deployment, the shared thread session mode is always on. If MAX_WORKERS >= MAX_SESSIONS, the actual effective value of MAX_WORKERS is taken as the smaller of the two.

    - In ISC Distributed Cluster Deployment, it is recommended that the MAX_WORKERS configuration on a single MN and DN equals the sum of the configurations on all CNs.

- **[MAX_SESSIONS](../../Reference Manual/Configuration Parameters.md#MAX_SESSIONS)**: The maximum number of session resources that can be requested, covering scenarios that consume session resources such as user connection sessions, parallel execution, and certain database background threads.

    Configuration suggestions/requirements for this parameter value: In ISC Distributed Cluster Deployment, it is recommended that the MAX_SESSIONS configuration on a single MN and DN equals the sum of the configurations on all CNs.

> **Note**:
>
> Client requests in transactions will occupy shared threads indefinitely. Transactions exceeding the number of shared threads that do not commit will block the processing of tasks from other connections. If such situations occur, please use the reserved emergency connection resources for emergency handling.

### Session Mode Management Parameter Modification Methods

To modify the session management parameters, execute the [ALTER SYSTEM SET](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM.md#setparameterclause) statement to set the corresponding parameter value and restart to take effect.

## Session Connection Information View

When using shared thread session mode, the GV$SESSION_WORKER and V$SESSION_WORKER views will record summary information about the shared thread pool.

In ISC Distributed Cluster Deployment, the connection between sessions across various nodes can be determined by the GLOBAL_SESSION_ID field of the GV$SESSION view. The GV$DATA_CONNECTION view can be used to obtain existing session connection information, primarily the relationships between CN and various established connection nodes.