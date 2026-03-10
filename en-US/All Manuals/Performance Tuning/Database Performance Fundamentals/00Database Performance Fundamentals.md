As the data carrier and underlying platform of application systems, database performance plays a decisive role in the overall quality of system operation. With the rapid development of technology, the geometric expansion of data volume and the exponential increase of concurrency have placed extremely high demands on database performance.

Many factors determine database performance, such as the application model running in the database, the hardware environment of the deployed database, and the configuration parameters of the database. Typically, it is difficult for a database to directly meet ideal performance requirements, necessitating performance optimization from different dimensions. This process is referred to as database performance tuning.

Performance tuning can be divided into initialization tuning and runtime tuning based on the time point at which tuning intervenes in the database state.

For example, when designing an application system, it is essential to simultaneously design for database performance. Users must consider various factors such as hardware configuration, software capabilities, preset data volume, and concurrency, in order to reasonably construct the software and hardware architecture and the database's initialization configuration.

Moreover, to ensure that the application system continues to operate efficiently in the face of changing environments, database optimization personnel must continuously monitor the performance status of the database and, if necessary, perform database performance tuning from the following two levels:

- Instance Level: Identify bottlenecks in the database instance as a whole and eliminate these bottlenecks through corresponding optimization measures to address performance issues.
- SQL Level: Optimize application SQL to reduce execution overhead and enhance application performance effectively with high-performance SQL statements.

The aforementioned tuning measures are not necessarily fixed at a specific time period of the database. For instance, a configuration parameter that is tuned during the initialization phase may still be identified as unreasonable during database operation and require further tuning.

This chapter will introduce the above concepts related to database performance tuning. Subsequent chapters will present specific measures and methods provided by YashanDB to achieve these tuning goals.