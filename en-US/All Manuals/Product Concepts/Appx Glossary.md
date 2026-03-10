Alphabetical Index:
[A](#apara)		[B](#bpara)		[C](#cpara)		[D](#dpara)		[F](#fpara)		[G](#gpara)		[H](#hpara)		[I](#ipara)		[J](#jpara)		[K](#kpara)		[L](#lpara)		[M](#mpara)		[N](#npara)		[O](#opara)		[P](#ppara)		[R](#rpara)		[S](#spara)		[T](#tpara)		[U](#upara)		[V](#vpara)		[W](#wpara)		[X](#xpara)		[Y](#ypara)		[Z](#zpara)		[2](#2para)

<span id="apara" name="apara" class="yaslink"></span>

### AAP

Adaptive Asynchronous Parallel

Adaptive Asynchronous Parallel Strategy: The communication strategy used by YashanDB in ISC Distributed Cluster Deployment.

### AC

Access Constraint

AC: A database object uniquely created by YashanDB based on bounded computation theory, achieving scale-independent queries and significant acceleration in query efficiency during massive data analysis through bounded equivalent substitution of access AC.

### ACID

Atomicity, Consistency, Isolation, Durability

Atomicity, Consistency, Isolation, Durability: The four features that transactions should have in a reliable database system, an industry-standard ACID concept.

### Ad-hoc

Ad hoc queries

Ad hoc queries: Queries defined flexibly by users, where the system automatically generates statistical reports based on the conditions.

### AES

Advanced Encryption Standard

Advanced Encryption Standard: The most popular symmetric encryption algorithm, using the same key for both encryption and decryption. AES128, AES192, and AES256 represent the key grouped by 128-bit, 192-bit, and 256-bit, respectively.

### ASCII

American Standard Code for Information Interchange

American Standard Code for Information Interchange: A character set supported by YashanDB, based on a coding system of Latin letters, primarily used to display modern English and other Western European languages. It is the most universal information exchange standard and is equivalent to the international standard ISO/IEC 646.

### ASN

Archive Sequence Number

Archive sequence number: The sequence number of the archived file, incremented by one each time the redo file switches.

### AU

Allocation Units

Allocation Unit(s): The minimum unit for disk space allocation in YashanDB's YFS.

<span id="bpara" name="bpara" class="yaslink"></span>

### BTREE Storage Structure

Balanced Tree storage

B-tree (Balanced Multi-way Tree) storage structure: A common self-balancing tree data structure used to organize and store ordered data, where data is written in order based on Key values to improve query efficiency.

### BTree Index

BTree Index

B-tree index: An index based on the B-tree data structure, used to accelerate data retrieval operations in database systems. B-tree index is the default index type in YashanDB.

<span id="cpara" name="cpara" class="yaslink"></span>

### CBO

Cost-Based Optimizer

Cost-Based Optimizer: An optimizer responsible for generating SQL execution plans in a database, where the CBO decides the optimal execution plan based on the cost of the query.

### CDC

Change Data Capture

Change Data Capture: A technology for capturing changes in a database. Through CDC technology, users can monitor and capture changes such as insertions, updates, and deletions in the database, and then record these changes for further processing or analysis.

### CGROUP

Control Groups

Resource control group: A mechanism provided by the Linux kernel to group a set of tasks and their subtasks into different categories and allocate the required resources accordingly.

### CMS

Cluster Management Service

Distributed Cluster Management Service: A distributed cluster management service provided by YashanDB in ISC Distributed Cluster Deployment.

### CN

Coordinator Node

Coordinator Node: In YashanDB's ISC Distributed Cluster Deployment, the CN adopts a multi-active architecture, responsible for providing interfaces, receiving user requests, generating distributed query plans, distributing query plans to DN, and summarizing execution results.

### CTE

Common Table Expression

Common Table Expression: A subquery in a SQL statement that can be referenced multiple times, known as a CTE. For example, a subquery defined after WITH in a SELECT statement is a CTE, which can be referred to multiple times in subsequent statements and can be used to create recursive queries.

<span id="dpara" name="dpara" class="yaslink"></span>

### DC

Dictionary Cache

Dictionary Cache: A buffer domain in YashanDB used to store metadata of database data and objects, which can accelerate data access.

### DDL

Data Definition Language

Data Definition Language: In the ANSI SQL standard, SQL is divided into DDL, DML, DCL, etc., where DDL SQL statements are used to manipulate objects and attributes within the database.

### DERM

Distribute Execute Resource Manager

Distributed Execution Resource Manager: A module used for resource management in YashanDB's ISC Distributed Cluster Deployment.

### DG

DiskGroup

Disk group: A logical unit for managing disk devices in YashanDB's YFS.

### DH

Diffie-Hellman

Key exchange protocol: DH is used to ensure secure key exchanges between communicating parties, required by SSL connections.

### DIN

Distribute Internal Network

Distributed Internal Network: The internal network used for inter-node access in YashanDB's ISC Distributed Cluster Deployment.

### DML

Data Manipulation Language

Data Manipulation Language: In the ANSI SQL standard, SQL is divided into DDL, DML, DCL, etc., where DML SQL statements are used to manipulate the data contained in database objects, i.e., records.

### DN

Data Node

DN: In YashanDB's ISC Distributed Cluster Deployment, DN is responsible for storing data and executing query plans issued by CN. The DN group provides high availability, with master-slave relationships among nodes, achieving data consistency between nodes using the Raft protocol.

### DXG

Data Exchange

Network data exchange operator: The capability in YashanDB's ISC Distributed Cluster Deployment of reorganizing and computing data in the distributed cluster during execution.

<span id="fpara" name="fpara" class="yaslink"></span>

### FG

FailureGroup 

Failure group: A logical unit implemented by YashanDB's YFS for high availability of disk data.

<span id="gpara" name="gpara" class="yaslink"></span>

### GBK

Chinese Internal Code Specification

Chinese Internal Code Specification: A character set supported by YashanDB, which employs variable-length encoding using single and double bytes, with English using single-byte encoding completely compatible with ASCII and Chinese parts using double-byte encoding.

### GCS

Global Cache Service

Global Cache Service: A global cache service in YAC, which realizes the sharing of data blocks between instances.

### GLS

Global Lock Service

Global Lock Service: In YAC, the global lock service is responsible for concurrency control between instances.

### GRC

Global Resource Catalog

Global Resource Catalog: In YAC, the global resource catalog registers the status information of all data blocks and lock resources.

### GTID

Global Transaction ID

Global transaction number: Used to identify globally unique distributed transactions.

### GTS

Global Time Service 

Global time service: The global time management service in YashanDB's ISC Distributed Cluster Deployment.

<span id="hpara" name="hpara" class="yaslink"></span>

### HA

High Availability

High availability: Refers to the system's ability to execute its functionality without interruption. The primary-standby replication architecture of YashanDB is a type of high availability capability.

### HTAP

Hybrid Transaction and Analytical Process

Hybrid Transaction and Analytical Processing: A new type of database that breaks the barrier between OLTP and OLAP, meeting the needs for supporting both transactional and analytical business scenarios in the same database.

### HWM

High Water Mark

High Water Mark: In a database, HWM is used to mark the upper boundary of a memory area that has been used, such as the highest point of data stored in a Segment

<span id="ipara" name="ipara" class="yaslink"></span>

### ICS

Interconnect Communication Service

Internal Communication Service: A service in YAC that provides internal communication between instances.

### ISC Distributed Cluster

Integration Storage and Compute Distributed Cluster

Integration Storage and Compute Distributed Cluster:  A distributed architecture implemented and a deployment form provided by YashanDB .

### ISO-8859-1

ISO-8859-1 (Latin1)

ISO-8859-1 (Latin1): A character set supported by YashanDB, which is a single-byte encoding compatible with ASCII.

<span id="jpara" name="jpara" class="yaslink"></span>

### JDBC

Java Database Connectivity

Java Database Connectivity: The interface defined in the Java language for operating databases and executing SQL statements. YashanDB JDBC is the JDBC driver provided by YashanDB, allowing users to connect to the database and perform operations and execute SQL statements.

### JSON

JavaScript Object Notation

JavaScript Object Notation: JSON is a lightweight data exchange format written in text using JavaScript object notation.

<span id="kpara" name="kpara" class="yaslink"></span>

### Currying

Currying

Currying: A functional programming technique that transforms a function accepting multiple parameters into a sequence of functions, each accepting a single parameter, and returning a new function that accepts the remaining parameters and returns a result.

<span id="lpara" name="lpara" class="yaslink"></span>

### LBAC

Label-Based Access Control

LBAC: Controls data access based on the security label of the user and the security label of the data, precisely controlling the user's read and write privileges on each row of data in the table.

### LCR

Log Change Record

Log Change Record: Usually used to describe change records in the database transaction logs, recording modification operations to data in the database, including insertions, updates, and deletions. LCR can be used for heterogeneous database replication and log analysis.

### LFN

Log Flush Number

Log Flush Number: A globally incrementing number that indicates the number of times redo flush has been performed in the database. This number increments by one each time a redo flush is executed.

### LOB

Large Object

Large Object: A data type supported by YashanDB, with BLOB being a binary large object and CLOB being a character large object.

### LRU

Least Recently Used

Least Recently Used: A memory eviction algorithm used in YashanDB.

### LSC

Large-scale Storage Columnar Table

LSC table: A type of table in YashanDB used for storing massive data, providing a low-cost solution for managing vast amounts of data and supporting real-time storage analysis and updating of massive data.

### LZ4

A relatively general-purpose compression algorithm known for its efficiency.

<span id="mpara" name="mpara" class="yaslink"></span>

### MCOL

Mutable Columnar Storage

Mutable Columnar Storage: A storage structure in YashanDB applied to TAC and LSC tables.

### MDS

MetaData Service

Metadata Service: A metadata management service provided by YashanDB in ISC Distributed Cluster Deployment.

### MMS

Memory Mapped Space

Memory Mapped Tablespace: A special tablespace in YashanDB used in in-memory databases. Compared to ordinary tablespaces, the data file corresponding to MMS blocks remains resident in memory pages. MMS supports preloading upon database startup, loading all table space data into memory.

### MN

Management Node

MN: In YashanDB's ISC Distributed Cluster Deployment, MN is responsible for managing distributed cluster nodes, metadata management services, and distributed transaction services. Nodes within the MN group have a master-slave relationship and achieve inter-node consistency through the Raft protocol.

### MVCC

Multi-Version Concurrency Control

Multi-Version Concurrency Control: A concurrency control method that represents concurrent access to the database, allowing read-write and write-read operations of different transactions to be executed concurrently while maintaining data consistency. YashanDB is also a database based on MVCC to implement concurrent transaction capabilities.

<span id="npara" name="npara" class="yaslink"></span>

### NUMA

Non-Uniform Memory Access

Non-Uniform Memory Access: An operating system architecture that improves storage access performance by providing separate storage to each processor.

<span id="opara" name="opara" class="yaslink"></span>

### OLAP

Online Analytical Processing

Online Analytical Processing: A database form focused on data analysis, supporting various complex analysis operations on massive data, used for decision support, such as data warehouses.

### OLTP

Online Transactional Processing

Online Transaction Processing: A database form focused on transaction processing that requires providing processing results for user requests in a very short time.

<span id="ppara" name="ppara" class="yaslink"></span>

### PITR

Point-In-Time Recovery

Point-In-Time Recovery: Using backup sets and archive log files to restore to any point in time after the backup, allowing for rollback of database operations to resolve data damage caused by erroneous operations.

### PL

Procedural Language

Procedural SQL Language: A procedural programming language built on standard SQL.

### PX

Parallel Execution

Parallel Execution Operator: In YashanDB's distributed execution, the ability to reorganize and compute data across the distributed cluster. In a parallel environment, the ability to reorganize and compute data across multiple threads.

<span id="rpara" name="rpara" class="yaslink"></span>

### RBO

Rule-Based Optimizer

Rule-Based Optimizer: An optimizer responsible for generating SQL execution plans in a database, where RBO decides the optimal execution plan based on specified rules.

### RLE

Run-Length Code

Run-Length Encoding: A data encoding method that replaces consecutive strings of identical values with a value and length, thereby achieving compressed storage of data.

### RPC

Remote Procedure Call Protocol

Remote Procedure Call Protocol: The protocol used by some internal components of YashanDB for communication.

<span id="spara" name="spara" class="yaslink"></span>

### S3

Amazon Simple Storage Service

Amazon Simple Storage Service: An online storage service provided by Amazon using its Amazon Web Services system.

### SCN

System Change Number

System Change Number: A single, continuously incrementing sequence in the database that indicates changes in the database's consistency state. YashanDB assigns a unique SCN to each committed transaction.

### SCOL

Stable Columnar Storage

Stable Columnar Storage: A storage structure in YashanDB used in LSC tables.

### SCSI

Small Computer System Interface

Small Computer System Interface: A storage technology based on the SCSI standard protocol.

### SIMD

Single Instruction Multiple Data

Single Instruction Multiple Data: A technology that executes the same instruction on a batch of data at the same time, performing parallel computations to improve efficiency.

### SM4

A symmetric encryption algorithm issued by the National Cryptography Administration, used to replace international algorithms such as DES/AES. It uses the same key for encryption and decryption, with keys grouped in 128 bits.

### SQL

Structured Query Language

Structured Query Language: An ANSI standard computer language used to access and manipulate database systems.

### SSL

Secure Socket Layer

Secure Socket Layer Protocol: A global standard protocol for encrypted communication between client and server.

### SSM

Segment Space Management

Segment Space Management: A method used in segment-page storage mode for managing segment spaces.

<span id="tpara" name="tpara" class="yaslink"></span>

### TAC Table

Transaction Analytics Columnar Table

TAC Table: A table type in YashanDB focused on real-time analysis scenarios, used to provide HTAP capabilities.

### TAF

Transparent Application Failover

Transparent Failover: A client functionality that drives automatic reconnection to the database in the event of a failure, aiming to minimize disruptions to end-user applications caused by instance or network failures.

### TDE

Transparent Data Encryption

Transparent Data Encryption: A technology that can encrypt data on storage media while being completely transparent for internal database access, ensuring data security without affecting database functionality and user use.

### TM

Transaction Manager

Distributed transaction management: A transaction manager defined in the XA protocol that is responsible for overall coordination in distributed transactions.

### TPC

Transaction Processing Performance Council

Transaction Processing Performance Council: A non-profit organization that defines benchmark tests like TPC-C, TPC-H, and TPC-DS, releasing objective performance data based on these benchmark tests.

### TPC-C

TPC-C

An industry-standard benchmark measuring the performance of online transaction processing (OLTP) systems.

### TPC-H

TPC-H

An industry-standard benchmark measuring the performance of online analytical processing (OLAP) systems.

### TPC-DS

TPC-DS

The next-generation decision support system test benchmark launched by the TPC organization to replace TPC-H.

<span id="upara" name="upara" class="yaslink"></span>

### UDF

User-Defined Function

User-Defined Function: Functions created through user programming, representing a procedural object in the database.

### UDS/IPC

UNIX Domain Socket/Inter-Process Communication Socket

Inter-process communication mechanism on the same server: Unlike inter-process communication between servers using "IP address + port number," UDS uses the filesystem to complete inter-process communication.

### UTF-8

8-bit Unicode Transformation Format

A variable-length character encoding for Unicode, Unicode: A character set supported by YashanDB, which encodes Unicode with 1, 2, or 3 bytes, widely used due to its compatibility with ASCII.

<span id="vpara" name="vpara" class="yaslink"></span>

### VM

Virtual Memory

Virtual Memory: A memory area used in SQL execution processes for resource-intensive tasks like materialization and sorting.

<span id="wpara" name="wpara" class="yaslink"></span>

### WAL

Write Ahead Logging

Write Ahead Logging: A mechanism in YashanDB for implementing data persistence, where data modifications are not directly written to disk but logged into redo logs, which are only written to disk upon transaction commit.

<span id="xpara" name="xpara" class="yaslink"></span>

### XA

eXtended Architecture

Distributed transaction: XA is a specification for distributed transaction processing, used in YashanDB to represent distributed transactions.

### XRM

Transaction resource manager

Transaction Resource Manager: Used in YashanDB to manage resources for transaction processing.

<span id="ypara" name="ypara" class="yaslink"></span>

### YCK

Yashan Cluster Kernel

Yashan Cluster Kernel: The core component of YAC, aggregating concurrent access to data and non-data resources by multiple instances.

### YCS

Yashan Cluster Service

Yashan Cluster Service: In YAC, the cluster service is responsible for managing YAC database instances and filesystem resources.

### YCR

Yashan Cluster Register

Yashan Cluster Register: In YAC, the YCR is responsible for storing configuration information for cluster services, including server configurations, resource configurations, etc.

### YEX

YashanDB external agent

An agent used to execute external UDFs.

### YFS

Yashan File System

Yashan File System: In YAC, the filesystem is responsible for managing storage devices and providing file system-like interfaces for database usage.

<span id="zpara" name="zpara" class="yaslink"></span>

### ZSTD

Zstandard

A fast lossless compression algorithm that achieves both high compression ratios and high compression performance.

<span id="2para" name="2para" class="yaslink"></span>

### 2PC

Two-phase Commit

Two-phase Commit: When a transaction spans multiple nodes, a coordinating component is needed to unify control over the operation results of all nodes (referred to as participants) and ultimately instruct these nodes whether to truly commit the operation results to maintain the ACID features of the transaction.