## Performance Report Analysis

The performance report provided by YashanDB mainly includes the following content:

-  Database Information
- Server Information
- Snapshot Information
- Load Information
- TOP10 Wait Events
- Wait Class Information
- SQL Information sorted by Execution Time, CPU Time, I/O Wait Time, Execution Count, Parse Count, etc.
- Information on spinlock spin counts within a specified time

Users can analyze the above content from the following aspects:

- Identify Slow SQL: When the system executes SQL slowly, users can view SQL ordered by Elapsed Time to find the most time-consuming SQL.
- Identify Time Loss Between Two Snapshots: By checking the TOP10 wait times, users can see how much time each event takes.
- Identify Read/Write Size of Memory and IO Between Snapshots: By checking load information, users can see the corresponding information.
- Check SQL Reuse Rate: By comparing "parses(SQL)" and "Hard parses(SQL)", a larger difference indicates a higher reuse rate and greater efficiency.
- Check Spinlock Spin Counts: By comparing the size of times when spincount is the same, a larger times suggests more intense lock contention.

## Statistical Item Analysis

Users can analyze performance bottlenecks in the system based on the statistical items provided by YashanDB, and additionally verify the effectiveness of tuning operations by observing changes in statistical items afterward.

In YashanDB, each statistical item reflects a specific metric of a component. The refinement of metrics helps users identify the root cause of performance issues, as performance problems often manifest through abnormalities in multiple statistical indicators.

Taking common IO issues as an example, an increase in system IO load is usually caused by rising business pressures or background thread workloads. Users can identify corresponding problems by observing changes in different statistical items for targeted optimization:

-  If DISK READS and DISK READ TIME are increasing rapidly, it indicates that the database is frequently reading data blocks. This phenomenon is mainly caused by the business accessing a large amount of data in a short time or a too small DATA_BUFFER_SIZE leading to frequent buffer evictions. Users can attempt to increase DATA_BUFFER_SIZE or optimize unnecessary data scans (for example, optimizing full table scans to index scans) to reduce IO pressure.
- If VM SWAP OUT is rapidly increasing, it indicates that the database is generating a large number of intermediate computation results (such as sorting operations), while the configured VM_BUFFER_SIZE is small, causing frequent swapping in and out. Users can try increasing VM_BUFFER_SIZE to alleviate this.
- If CHECKPOINTS COMPLETED is rapidly increasing, this is the additional IO growth brought by the database's background cleaning of dirty blocks and generally does not affect the business.
- If REDO SYNCH WRITE is rapidly increasing, it indicates that the transaction commit frequency of the business is high, and the flushing of redo logs is causing IO growth, which is a normal phenomenon. However, if this situation leads to excessively high IO load and noticeable degradation in business performance, it is necessary to analyze whether there is a bottleneck in the performance of the storage medium.

For specific descriptive information about all statistical items in YashanDB, please refer to the reference manual in the [statistics](../../Reference Manual/Statistics Information) section.