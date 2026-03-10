When the client or driver interface connects to the YashanDB database server, the connection listener will create or assign a corresponding thread for each client connection to handle that client connection session.

## Session Mode

YashanDB supports the following session modes:

- Exclusive Thread Session Mode: This mode creates an independent working thread for each client connection, dedicated to serving that session. The corresponding working thread will be destroyed when the session disconnects.

- Shared Thread Session Mode: This mode manages session working threads using a thread pool. When a new client connection is accepted, an available thread from the thread pool will be allocated to handle that session connection. After the session is completed, the thread will return to the thread pool, waiting for new client session connections.

## Switching Session Modes

The session mode is controlled by the configuration parameter MAX_REACTOR_CHANNELS. A value of 0 indicates exclusive thread session mode, while a non-zero value indicates shared thread session mode.

In standalone and YAC Deployment, the default mode is exclusive thread session mode (MAX_REACTOR_CHANNELS = 0).

In ISC Distributed Cluster Deployment, CN nodes must use shared thread session mode (MAX_REACTOR_CHANNELS > 0, default is 1) to handle tasks from client connections, while MN and DN node groups do not need to configure this parameter.

To switch the session mode, execute the [ALTER SYSTEM SET](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM.html#setparameterclause) statement to set the corresponding parameter value and restart to take effect.

When using shared thread session mode, session-related configurations/behaviors are also affected by the following parameters. Please configure the related parameter values reasonably according to actual needs:

- **[MAX_REACTOR_CHANNELS](../../Reference Manual/Configuration Parameters.html#max_reactor_channels)**: The number of background threads responsible for listening for network connections in a waiting state.

    Configuration suggestions/requirements for this parameter value: In ISC Distributed Cluster Deployment, CN nodes must configure MAX_REACTOR_CHANNELS to a non-zero value.

- **[MAX_WORKERS](../../Reference Manual/Configuration Parameters.html#max_workers)**: The maximum number of threads in the shared thread pool, which is the maximum number of concurrent tasks in the database.
    
    Different behaviors and configuration suggestions/requirements of this parameter value:

    - When MAX_WORKERS is set to 0, the actual effective value of MAX_WORKERS is calculated as CPU count * 2.

    - In standalone and YAC Deployment, when MAX_WORKERS >= MAX_SESSIONS, the thread pool is closed (even if MAX_REACTOR_CHANNELS > 0, it will not take effect).

    - In ISC Distributed Cluster Deployment, the shared thread session mode is always on. If MAX_WORKERS >= MAX_SESSIONS, the actual effective value of MAX_WORKERS is taken as the smaller of the two.

    - In ISC Distributed Cluster Deployment, it is recommended that the MAX_WORKERS configuration on a single MN and DN equals the sum of the configurations on all CNs.

- **[MAX_SESSIONS](../../Reference Manual/Configuration Parameters.html#MAX_SESSIONS)**: The maximum number of session resources that can be requested, covering scenarios that consume session resources such as user connection sessions, parallel execution, and certain database background threads.

    Configuration suggestions/requirements for this parameter value: In ISC Distributed Cluster Deployment, it is recommended that the MAX_SESSIONS configuration on a single MN and DN equals the sum of the configurations on all CNs.

> **Note**:
>
> Client requests in transactions will occupy shared threads indefinitely. Transactions exceeding the number of shared threads that do not commit will block the processing of tasks from other connections. If such situations occur, please use reserved connections for emergency processing.

## Session Connection Information View

When using shared thread session mode, the GV$SESSION_WORKER and V$SESSION_WORKER views will record summary information about the shared thread pool.

In ISC Distributed Cluster Deployment, the connection between sessions across various nodes can be determined by the GLOBAL_SESSION_ID field of the GV$SESSION view. The GV$DATA_CONNECTION view can be used to obtain existing session connection information, primarily the relationships between CN and various established connection nodes.