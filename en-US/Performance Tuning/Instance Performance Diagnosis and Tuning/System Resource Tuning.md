The purpose of instance tuning is to reduce the resource consumption and running time required for execution, thereby improving the utilization of specific resources. Typically, instance-level performance issues arise from the excessive use of certain resources, which become bottlenecks in the system.

After analysis and diagnosis, users may find that often the root cause of performance issues comes from the resource capabilities of the system, or that enhancing the system's resource capabilities can effectively resolve certain performance problems. Database performance is closely related to the resource allocation of the operating system, such as memory, I/O, and network. Tuning these resources can greatly help improve database performance.

## I/O Performance Tuning

I/O performance is critical to database performance. For example, the flushing of redo data and dirty blocks directly depends on the I/O performance of the underlying disks. When deploying a database, one should choose media that meet the I/O performance requirements as the underlying storage for the database.

Usually, the evaluation indicators for disk I/O performance mainly include the following aspects:

- Throughput: the size of I/O requests per second
- IOPS: the number of I/O requests per second
- Response Time: the time interval from when an I/O request is issued until a response is received
- Utilization: the percentage of time the disk is processing I/O

> **Note**: 
>
> The above I/O performance evaluation indicators can all be obtained through I/O tools (such as IOSTAT).

Due to the specific characteristics of the database's I/O model, users need to further evaluate these characteristics to determine whether the disks meet the requirements of the database application, focusing on the following aspects:

- Discrete I/O Capability: The reading and flushing of data blocks involve discrete I/O. If the disk has poor discrete I/O performance, it will severely affect database performance.
- Different I/O Size Capability: The default data block size for YashanDB is 8K, while the I/O size for redo is generally 1M. Other settings, such as archiving and backup, have different I/O size configurations.
- I/O Parallel Capability: The database I/O model is a mixed read/write model, thus requiring a high level of mixed I/O capability from the disk.

It is recommended that users validate the I/O for these scenarios using I/O testing tools (such as FIO) before deploying the database, to ensure that there will not be significant I/O bottlenecks during the database operation.

When noticeable I/O bottlenecks occur during database operation, and there are no plans to enhance resource capabilities, I/O performance tuning should be considered. Some typical measures include:

- Deploying redo files and DATA files on separate disks to reduce I/O contention.
- Modifying database I/O behavior through DATAFILE_IO_MODE and REDOFILE_IO_MODE to meet the operational environment.
- Adjusting DBWR series parameters to improve I/O efficiency.
- Increasing VM BUFFER to reduce SWAP.

## CPU Performance Tuning

CPU performance also affects database performance. When deploying a database, users generally need to pay attention to the following CPU indicators that may affect performance:

- CPU Cores: Determines the system's concurrency processing capability, and also affects the database's concurrency processing capability, while considering the parallelism of other applications in the system.
- CPU Clock Speed: Determines the execution capability of the CPU, which in turn affects the execution capability of the database.
- CACHE: YashanDB has many optimization mechanisms for CACHE, and a larger CACHE can yield significant performance benefits.
- NUMA: Accessing memory across NUMA nodes can introduce high latency, which is not friendly to such memory access models as databases. It is advisable to minimize the number of NUMA nodes.

> **Note**: 
>
> - The above CPU performance evaluation indicators can all be obtained through operating system tools such as lscpu.
> - It is generally suggested that the number of parallel processes in the database does not exceed twice the number of CPU cores; otherwise, frequent context switching will severely impact database performance.

When CPU bottlenecks occur during database operation (e.g., high system LOAD), the following optimizations are usually considered:

- Database Application Optimization: Tune SQL to select better execution plans and reduce resource overhead.
- Database Throttling: Control database workflow by changing thread pool configurations.
- Other Application Optimization: As CPU is a shared resource, optimizations should also be considered for applications other than the database.
- Hardware Upgrade: If current hardware does not meet application demands, consider upgrading the hardware.

## Memory Performance Tuning

Memory is one of the important operating resources for the database. The following aspects should be considered for optimization:

- Memory Size: Configure a reasonable memory size for the database, such as the DATA BUFFER POOL. This memory is crucial for the overall performance of the database.
- Memory SWAP: It is advisable to disable the SWAP area to prevent performance fluctuations caused by memory swapping. YashanDB offers the option to use large pages of the operating system's memory to eliminate performance fluctuations caused by SWAP.
- Prevent OOM: It is recommended to turn off over_commit for system memory requests to prevent unexpected OOM occurrences during database operation.

When diagnosing memory issues, users can also utilize some system tools. For example, Page Faults caused by virtual memory management can severely impact database access efficiency. If too many Page Faults are detected, timely memory optimization should be performed.