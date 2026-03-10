## Deployment Architecture

YashanDB supports three types of deployment forms: **Standalone Deployment** (also known as primary/standby deployment), **YAC Deployment**, and **ISC Distributed Cluster Deployment**.

<span id="deploy" name="deploy" class="yaslink"></span>
![](./image/deploy.png)

### Standalone Deployment

Standalone Deployment typically runs the primary instance and standby instance on two separate servers. Replication is used to synchronize modifications from the primary database to the standby database. In some scenarios where high availability requirements are lower, only one server is used to run a single instance during deployment.

Standalone Deployment is a common form and is suitable for most scenarios.

### YAC Deployment

YACs at the hardware level rely on shared storage, allowing all instances to read and write. Instances exchange data through a global buffer.

YAC Deployment is commonly applied in high-end core transaction scenarios that require multiple write capabilities, high availability, performance, and scalability.

### ISC Distributed Cluster Deployment

ISC Distributed Cluster Deployment includes a greater variety of subsystems, such as Metadata DN Management (MN Group), Coordinator Node Management (CN Group), and DN Management (DN Group). Multiple types of subsystems can run simultaneously on the same server.

ISC Distributed Cluster Deployment is suitable for scenarios that require high processing capabilities and strong linear scalability demands, such as massive data analysis applications.

## Logical Architecture

The zero-level view of YashanDB's logical architecture is shown in the following figure:

![](./image/logicview.png)

### Major Subsystems of Standalone Database

**Client Driver**

Includes a series of client APIs that provide capabilities such as establishing connections, executing SQL statements, and obtaining result sets.

**SQL Engine**

The SQL engine includes a parser, optimizer, and executor, responsible for parsing the SQL text submitted by the client, generating execution plans, and executing them. The SQL engine provides a rich built-in function library for convenient use of functions in SQL for expression operations.

**PL Engine**

The PL engine offers capabilities for user-defined functions (UDFs), type management, and user-defined types (UDTs), including advanced packages, stored procedures, stored functions, and triggers. PL objects can be persisted and can be executed multiple times after creation.

**Storage Engine**

Responsible for storage space management using a three-level management approach of segments, pages, and blocks; handles transaction management and controls concurrent access, providing consistent access capabilities; manages relational objects, including tables, indexes, etc.

### Major Subsystems of YAC Database

In addition to the standalone form, YAC Deployment adds three subsystems: YAC kernel, file system, and YAC management:

**YAC Kernel**

The core component during the cluster's runtime in YAC Deployment, responsible for coordinating memory pages across servers during operation using cohesive memory technology, ensuring efficient and consistent access among multiple instances in the cluster.

**File System**

Responsible for managing storage devices, providing a file system-like interface for database use, and processing file read/write requests from upper-level applications, converting them into read/write operations on storage devices through address translation.

**YAC Management**

Responsible for managing the cluster and providing configuration management capabilities in YAC Deployment.

### Major Subsystems of ISC Distributed Cluster Database

In addition to the standalone form, ISC Distributed Cluster Deployment adds three subsystems: Metadata DN Management, Coordinator Node Management, and DN Management:

**Metadata DN Management (MN Group)**

MN is responsible for node management services, metadata management services, and global clock services in the distributed cluster.

**Coordinator Node Management (CN Group)**

CN processes client connection requests and SQL command requests, generates distributed execution plans, then distributes them to DN for execution, and finally aggregates and returns the execution results to the client.

**DN Management (DN Group)**

DN is responsible for persisting data and executing the decomposed SQL execution plans.

### Common Infrastructure Library

The infrastructure library contains commonly used public foundational capabilities, including network communication, thread management, etc.

## Instance Architecture

YashanDB includes two concepts: databases and database instances (referred to as "instances"). Databases and database instances generally have a one-to-one relationship, but in YAC Deployment, there is a one-to-many relationship between databases and database instances. For more details, please refer to [Deployment Architecture](#deploy).

- Database

    A database refers to a set of data files stored on non-volatile storage, including control files, data files, and log files. If these data files are missing or damaged, the database instance cannot start and operate normally.

- Database Instance

    A database instance exists only during runtime and consists of a set of memory structures and a multithreaded program. For more details, please refer to [Instance Architecture](../Instance Architecture/Database Instance).

In most cases, we use "database" to refer to both concepts mentioned above.