Typical SQL tuning involves the following steps:

1. Identify high-load SQL statements.

    Users can find statements that consume much time and system resources from performance views, performance reports, or logs.

2. Collect performance-related data, including statistics and metadata:

    - statistics: The optimizer statistics record information such as table data size and distribution. Statistics are crucial; if statistics are missing or outdated, the optimizer cannot generate the best execution plan.
    
    - metadata: Collect the structure of the tables and views that SQL needs to access, as well as the index definitions that may be used by the statement.

3. Determine the cause of the problem, common causes include:

    - Inefficient SQL execution

      If the written SQL performs unnecessary operations, the optimizer will not be able to improve its performance. For example: joins without join conditions (Cartesian products), specifying large tables as driving tables, using UNION instead of UNION ALL, executing subqueries for each row of the outer query, etc.

    - Suboptimal execution plans

      The optimizer selects the theoretically optimal plan among all possible execution paths, but this does not mean the optimizer will necessarily choose the optimal plan. Sometimes, the optimizer may select a plan with a less-than-ideal access path, such as using predicate conditions with low selectivity, possibly resulting in a full table scan on large tables instead of using an index.

    - Unreasonable data access structure

      The lack of indexes and unmaterialized views are typical reasons for poor SQL performance. The best access structure can exponentially improve SQL performance.

    - Outdated optimizer statistics

      When maintenance operations for statistics (automatic or manual) cannot keep up with table data changes caused by DML, the collected statistics may become outdated. Outdated statistics on tables cannot accurately reflect the distribution of table data, which may lead the optimizer to make decisions based on incorrect information and generate poor execution plans.

    - Irreasonable database system parameters

      The parameters configured by tuning the database initialization are usually capable of meeting user needs, but with changes in the environment and data scale, the configuration parameters should be reasonably adjusted in a timely manner. For example, the size of the SGA affects memory allocation; if it is too small, it will impact the buffer hit rate, increase I/O load, and thus affect performance.

    - Hardware issues

      Performance issues are also related to CPU, memory, and I/O. Optimizing the allocation of system resources can enhance the performance of all SQL statements.

4. Define the scope of the problem.

    The scope of the problem-solving approach must match the scope of the issue. Users need to determine whether the problem is at the instance level or the SQL statement level. For example, if the shared pool is too small, it may cause plan buffers to be discarded, leading to many hard parses. In this case, increasing the size of the shared pool by adjusting configuration parameters can resolve the instance-level issue and improve the performance of all sessions. However, if a specific SQL statement is failing to use an effective index, changing the optimizer-related configuration parameters for the entire database may harm overall performance. Therefore, when a single SQL statement has issues, a statement-level solution should be used to address it.

5. Optimize execution performance.

    The following actions can effectively improve SQL execution efficiency, and users can adopt one or more of them based on their situations:

    - Rewrite SQL to improve efficiency

    - Use bind variables to reduce hard parses

    - Use equality join conditions

    - Do not use functions in filter conditions

    - Break down complex SQL into multiple simple statements and use parallel processing, etc.

    Additionally, performance can be enhanced by changing metadata objects, such as adding indexes, adjusting index orders, using partitioned tables, or even modifying data design.

6. Prevent SQL performance degradation.

    Regularly review performance reports or logs to ensure that the execution plans produced by the optimizer remain optimal.

For detailed SQL-level tuning guidance, please refer to the [SQL Tuning](../SQL Tuning/00SQL Tuning) section.