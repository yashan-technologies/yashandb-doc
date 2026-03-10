## Program Interface

The program interface is a necessary link between the application and the database, providing a software layer for database applications to access and operate on the database. The program interface possesses the following capabilities:

- Simplifies database operations, allowing complex database tasks to be completed through simple function calls.

- Enables cross-machine access, solving data format conversion and data exchange between different machines.

- Sends application requests to the database instance according to the YashanDB communication protocol, while also returning the database instance's responses to the application.

**Database Drivers**

Database drivers are interfaces between the application and the database service. They translate operations in specific development languages into commands recognized by the database service, and also translate responses and data from the database service into usable data structures and content in that language.

YashanDB provides various drivers including JDBC, C, Python, ADO.NET, and ODBC to meet common application development needs in the industry.

- JDBC Driver: JDBC (Java Database Connectivity) is a specification for connecting Java applications to databases. YashanDB provides the ability to access YashanDB using the JDBC specification for Java.

- C Driver: Provides an interface (C API) for connecting and operating the database through C applications.

- Python Driver: A Python extension module (yasdb) that supports the Python DB API protocol(opens new window), allowing general Python applications to connect directly to the YashanDB database.

- ADO.NET Driver: A core data access technology for .NET languages. With Yashandb.Data.YashandbClient namespace, the YashanDB ADO.NET driver implements most classes and interfaces of ADO.NET to enable .NET applications to access and manipulate 
YashanDB database data.

- ODBC Driver: ODBC (Open Database Connectivity) offers a standard API method to access databases, utilizing SQL to perform most tasks.

## Networking Architecture

YashanDB's network service facilitates communication capabilities between clients and database services, as well as between database services. In a typical database environment, applications and database service instances usually run on different machines. Through YashanDB's network service, applications can connect to database instances for operations, and data synchronization and internal task collaboration can occur between database instances.

### Client Connection Session

Typically, clients connect to the database service through the driver interface provided by the database. The database service instance first starts to a state where it can provide services normally, and after obtaining the communication address of the database service, the client connects to the connection listener of the database service through the driver interface.

#### Connection Listener

The connection listener is a worker thread inside the database instance dedicated to handling client connection requests.

The responsibilities of the connection listener include:

- Identifying the legality of client connections based on black-and-white lists.

- Assigning sessions to client connections.

- Preparing runtime resources for client connections.

After the connection listener assigns a session to the client connection, the session is responsible for subsequent communication interactions with the client session, while the connection listener continues to listen for the next client connection request.

#### Exclusive Thread Session Mode

In the exclusive thread session mode, the connection listener creates an independent worker thread for each client connection, specifically serving that client session. Each request sent by the client receives a quick response, and the corresponding working thread is only destroyed when the session disconnects.

The system's session mode can be controlled by configuring the parameter MAX_REACTOR_CHANNELS, where a value of 0 indicates exclusive thread mode.

In exclusive thread mode, each worker thread can only be used by one session connection. When there are no requests to process, the thread enters a waiting state. When a new connection is received, the worker thread in the waiting state cannot serve the new connection, and a new thread must be created to handle it. Exclusive thread session mode demonstrates significant performance advantages with fewer connections, but with more connection sessions, it can consume a large amount of runtime resources, leading to longer execution times for tasks that lack sufficient resources, indirectly affecting the performance of the database service.

#### Shared Thread Session Mode

In shared thread session mode, the system manages worker threads in a thread pool manner. When a new client connection is accepted, an idle thread from the thread pool is assigned to handle that session connection. Once the session completes, the thread returns to the thread pool, waiting for new client session connections.

When the configuration parameter MAX_REACTOR_CHANNELS is a non-zero value, the system operates in shared thread session mode.

If the number of client connection sessions reaches the thread pool configuration MAX_WORKERS, it's possible for all threads in the thread pool to be in use. If a new client connection request is received at this time, it must wait in line until a session completes its transaction and releases a thread, at which point the session thread will be allocated in order for request processing.

Shared thread session mode has the following advantages:

- Reduces the number of threads, allowing runtime resources to execute requests alternately rather than exclusively idling.

- Reduces the memory resources required for worker threads to process requests.

- Expands the number of simultaneous connections allowed.

- When clients frequently connect or disconnect, the performance of shared thread session mode may outperform that of exclusive thread session mode.

However, shared thread session mode also has some drawbacks, such as potentially slower request responses when there are too many connection sessions and making troubleshooting more complex. It is recommended to consider enabling shared thread session mode only when there are relatively many connection sessions.

### Primary-standby Replication Network

The Replication Network is the communication network within the YashanDB database that facilitates data replication between the primary database and standby databases, as well as between standby databases and cascade standby databases. It is typically used to transmit redo logs, data files, etc., ensuring data consistency between database instances, with data flowing from the primary database to the standby database or from the standby database to the cascade standby database.

### Internal Interconnect Bus

The Internal Interconnect Bus (IN) is constructed to address the scenario of frequent communication involving massive data between numerous nodes within YashanDB, providing a high reliability, high performance, high throughput, and low latency network communication service.

#### Use Case

The Internal Interconnect Bus is primarily used in the following scenarios:

- The internal interconnect bus (CIN, Cluster Interconnect Network) in YAC Deployment facilitates network communication between database instances, YCS instances, and the YFS.

- The internal interconnect bus (DIN, Distributed Interconnect Network) in ISC Distributed Cluster Deployment is used for network communication among CN nodes, DN nodes, and MN nodes.

#### Connection Pool

The internal interconnect bus employs multiplexing technology, supporting multiple sessions to share one or more network links for communication. The internal interconnect bus encapsulates one or more links with the same communication functionality connected to a node within a connection pool, managing them in a pooled manner. Internal modules can select a random or specific link from the connection pool based on their own needs for message transmission.

#### Message Dispatching

Each link in the internal interconnect bus has a dedicated receiving listener thread for message reception and handling. Since messages are sent through the connection pool, multiple session messages may exist on the same link. The receiving listener thread dispatches the received messages to the corresponding session message queues based on the message type and session ID in the message header, which are then processed by the working threads of the sessions.

#### Separation of Data and Control Messages

Due to the partitioning of connection pools and internal thread resources, multiple connection pools on the same node are typically planned as separate channels with different functionalities. These channels are resource-isolated, not affecting one another. For example, the control channel is dedicated to sending and receiving control commands, involving small and few messages with high latency requirements, while the data channel is specifically for transmitting large amounts of data, involving large and frequent messages with high throughput requirements.