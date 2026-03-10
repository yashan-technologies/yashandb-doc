## Development History of Database Management Systems

**Database Management System (DBMS)** is a type of basic software used for the unified, reliable, and efficient management and organization of data, ensuring data security and integrity while providing high-efficiency data query capabilities.

A **Database** refers to a collection of data organized, stored, and managed according to a data structure.

> **Note**:
>
> The term Database often refers to the Database Management System (DBMS) as well, with a conceptual distinction made only when both appear simultaneously. This phenomenon may occur in this manual.

The first generation of Database Management Systems was mainly hierarchical and network-based. In hierarchical databases, data is organized in a tree structure (similar to a file system), while in network databases, data is organized into a many-to-many network structure.

In 1970, E.F. Codd first proposed the **relational model**, providing a unified and concise data model for database systems. Databases that conform to the relational model are referred to as **relational databases**, where data is stored in relation tables composed of rows (tuples of data) and columns (attributes). The corresponding DBMS is known as a **Relational Database Management System (RDBMS)**, which remains the most mainstream database management system today.

## Development History of YashanDB

The YashanDB database system, developed independently by the Shenzhen Institute of Computing Sciences, is a new type of database management system that integrates original bounded computing, approximate computing, parallel scalability, and cross-model fusion computing theories based on classic database theory. It meets the high-performance, high-concurrency, and high-security requirements of key industries such as finance, government enterprises, and energy.

The development of YashanDB can be divided into the following stages:

**2013-2018: Theoretical Proof and Foundation**

- First proposed the theory of big data computational complexity, laying the foundation for query complexity in big data.

- Original theoretical system: bounded computing, incremental computing, approximate computing, parallel computing, cross-model fusion, logic + AI, etc., establishing a theoretical foundation.

- Nearly a hundred A-class papers, awarded the best paper grand slam at top database conferences, with a decade-long verification award.

**2019-2022: Product and Engineering Implementation**

- The Shenzhen Institute of Computing Sciences was officially established.

- YashanDB completed the first phase of original theory, core technology, and self-developed system, achieving the capability for core system launch.

- The prototype of a high-end YAC was completed, verifying its feasibility.

- According to authoritative institutions, the autonomy rate of the kernel code is 100%.

- Awarded "Top Ten Hardcore Technologies" by Digital China.

- The product debuted at CNCC and was featured in a DeepTech interview.

**2023: Market Replication and Key Industry Penetration**

- YashanDB V22.2 was officially released, with comprehensive improvements in functionality, performance, and stability.

- YashanDB V23.1 was released, introducing three products: high-end YAC, distributed real-time data warehouse, and spatial database.

- The personal version of YashanDB was fully opened for download.

- Featured at the opening ceremony of the 25th China Hi-Tech Fair.

- Awarded the "2023 Digital Transformation Autonomous Innovation Solution" by the National Industrial Information Security Development Center.

- Included in the 2023 World Internet Conference Leading Technology Award's collection "The Charm of Technology."

- Penetrated key industries such as finance, party politics, and energy.

**2024: High-End Core 1:1 Alternative**

- YashanDB V23.3 was released, designed specifically for core scenarios with a 1:1 alternative solution.

- The enterprise edition of YashanDB was fully opened for download.

- YashanDB YAC successfully went into production, creating a model for independent innovation in the government sector database.

- Signed a contract with the Digital Currency Research Institute of the People's Bank of China to promote high-quality digital development in the financial sector.

- Received commercial encryption, EAL4+, and network-specific security certifications, with database security capabilities recognized by authorities.

- Won the first prize for progress in geographic information technology.

- Garnered honors as an excellent product and solution in innovation and creation in Guangdong Province.

## YashanDB Deployment Forms

- **Standalone Deployment** <br> Combines traditional relational database theory with innovative base engine technology, suitable for centralized transaction business scenarios, supporting primary/standby modes.

- **YAC Deployment** <br> A multi-active cluster based on shared storage, providing computing/storage expansion and finance-grade high availability, suitable for high-end core transaction scenarios.

- **Distributed Cluster Deployment** <br> A distributed processing system based on the integration and evolution of Standalone and YAC, suitable for trading, analysis, or mixed tranding/analysis scenarios, all of which require high availability and resilience.

## Core Features of YashanDB

- **Row Store/Column Store** <br> Supports HEAP heap table, TAC LSC table, LSC LSC table. <br> Supports LIST/RANGE/HASH/INTERVAL partition types and various combinations of secondary partition capabilities. <br> Supports vectorization calculation.

- **Transaction Management** <br> Supports full transaction ACID, fine-grained lock management, statement read-write consistency, and supports read committed and serializable transaction isolation levels, UNDO self-management, and multi-version concurrency control.

- **High-Performance Query** <br> Provides a cost- and rule-based optimizer model. <br> Supports MPP distributed execution mode, featuring data sorting, sparse indexing, prefetching and buffering, and data compression technologies, combined with SQL engine features like partition pruning, parallel queries, and predicate pushdown to deliver high-performance queries.

- **Data Replication** <br> Supports synchronous and asynchronous replication modes, providing multiple replication strategies such as maximize protection and maximize performance.

- **Backup and Recovery** <br> Offers physical and logical data backup capabilities, supporting full and secondary incremental backups, and supports point-in-time recovery (PITR) functionality.

- **High Availability** <br> Provides one-primary/multi-standby, cascade high availability capabilities, supporting manual switching and leader election.

- **Database Flashback** <br> Supports flashback query, flashback modification, and recycle bin flashback capabilities.

- **Generic SQL Capabilities** <br> Complies with ANSI SQL standards, supports common SQL syntax, provides a rich function library and data types, supporting high-performance PLAN operators.

- **PL** <br> Supports stored procedures, UDF, anonymous blocks, advanced packages, JOBs, triggers, etc.

- **Cohesive Memory** <br> Supports the core technology of cohesive memory for collaborative data page read and write access and various non-data-related concurrency control among different instances in the cluster database.

- **File System Capability** <br> Can directly manage raw disks to provide file system services, offering parallel file read and write capabilities for multi-node clusters in YAC Deployment.

- **Spatial Data Management** <br> Supports ST_GEOMETRY data type for storing and accessing geometric objects in accordance with the SFA SQL standard set by the Open Geospatial Consortium (OGC).

- **Online Scaling** <br> Supports online scaling to meet customer business development needs.

- **Multitenant Architecture**<br>Adopting the "database as tenant" design concept, it provides native database consolidation solutions for enterprise complex business systems.  

## Scenarios Applicable for YashanDB

### Online Transaction Processing

YashanDB's centralized transaction system focuses on the development of next-generation infrastructure software and hardware, using critical industry core application scenarios as a traction point, providing a high-performance, highly reliable database foundation to meet the high-concurrency needs of online transaction processing.

- **Extremely High-Performance Online Transactions** <br> Such scenarios impose strict requirements on the timeliness and accuracy of transaction processing, along with the need for highly reliable data protection. YashanDB possesses refined transaction management capabilities, integrating a powerful storage foundation with a high-speed SQL engine, achieving outstanding transaction processing performance while ensuring strong consistency of data.

- **24/7 Uninterrupted Service** <br> YashanDB's high-availability architecture allows real-time synchronization and persistence of data across multiple data centers through a multi-replica log synchronization mechanism and achieves online automatic switching between primary and standby via Raft protocol leader election, ensuring a stable and continuous system operation without user perception.

- **Centralized Control for Enterprises** <br> YashanDB's HTAP hybrid load form, based on "one data set, one engine," supports both online real-time transactions and real-time analyses simultaneously, providing capabilities for high-concurrency online throughput and real-time analysis of massive data.

- **High Availability Seamless Switching Scenarios** <br> Industries such as finance, telecommunications, and electricity have high availability requirements and often rely on high availability architectures, which depend on shared storage. YashanDB YAC Deployment offers the same high-availability architecture capability, where when a single point of failure occurs in the cluster, another node will take over, allowing for transparent switching for the client with RPO of 0 and RTO in just seconds, ensuring continuous operation of core business without interruption.

### Mass Data Analysis

YashanDB's distributed analysis system, based on bounded computing theory for instant analysis, focuses on solving database bottlenecks such as big data computing efficiency, data production and analysis, mass data storage costs, and data silos (3V: Volume, Velocity, Variety), providing users with flexible traditional data warehouse acceleration and a one-stop data warehouse solution.

- **Mass Stable Data Analysis** <br> YashanDB's LSC table organizes data through column storage structure, supporting the separation of cold and hot data, and offering highly compressed object storage. By employing data sorting, sparse indexing, and filter pushdown techniques, it enables high-performance queries over mass data while supporting data writes to the hot data area to improve transaction performance, as well as supporting silent conversion and fusion queries between hot and cold data areas, primarily targeting interactive analysis scenarios for mass stable data.

- **Ad-Hoc Interactive Self-Service Analysis Scenarios** <br> Perform interactive exploratory analysis of businesses, achieving second-level response query analysis experiences through cost-efficient columnar storage engines, vectorized execution engines, and efficient distributed algorithms, all based on fully self-developed technology.

- **Real-Time Personalized Recommendation Scenarios** <br> Conduct multi-dimensional analysis based on massive historical user behavior log data, supporting real-time log input while completing second-level calculations for historical and real-time tags, and enabling user segmentation through various tag combinations to improve marketing success rates.