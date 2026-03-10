By optimizing through initialization, including reasonable system architecture and database initialization configuration, the database instance may already possess a relatively good performance state in its initial condition, such as:

- Reasonable database memory has been configured.
- The I/O requirements of different database modules have been considered.
- Database performance has been improved by optimizing the operating system.

However, during the instance's operation, one must remain vigilant about potential bottlenecks that could lead to performance issues, continuously monitor, and promptly identify performance-related problems.

**Instance Performance Diagnosis**

The most effective way to diagnose performance is to establish a baseline. When performance issues arise, current information can be compared with the baseline to identify performance problems. Baseline data generally includes:

- Application statistics: Data monitored from the application side, such as transactions per second, response time, etc.
- Database statistics: Data monitored from the database, such as wait events, system statistics, etc.
- Operating system statistics: Data monitored from the operating system, such as I/O, CPU, network, etc.

**Instance Tuning**

Typically, each performance issue has many causes. The key to tuning is to diagnose the root cause of the performance problem, thereby taking more targeted and effective measures, such as:

- Slow physical I/O: This is mostly caused by poor performance of physical disks, but it may also be due to unoptimized SQL leading to unnecessary I/O.
- High lock contention: Most are caused by unreasonable application design, with only a few requiring modifications to database configuration.
- High CPU usage: This may be due to insufficient CPU resources, unoptimized SQL, or inefficient applications.

For detailed operational guidance on instance performance diagnosis and tuning, please refer to the [Instance Performance Diagnosis and Tuning](../Instance Performance Diagnosis and Tuning/00Instance Performance Diagnosis and Tuning) chapter introduction.