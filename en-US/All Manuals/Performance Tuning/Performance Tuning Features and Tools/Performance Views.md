The table below lists a series of performance views provided by YashanDB. Users can query these views to obtain some performance data from the database:

|View Name |View Description |
| :----------------------- | :----------------------------------------------------------- |
| V$SYSSTAT                | Used to view system-level statistics. This view contains information from different components and is an important way to analyze overall performance bottlenecks in the database instance. |
| V$SESSTAT                | Used to view session-level statistics. This view can be used to analyze performance bottlenecks for sessions. |
| V$REDOSTAT               | Used to view redo disk flushing speed, checkpoint advancement speed, free space in redo, etc. This view is important for analyzing checkpoint performance. |
| V$RECOVERY_PROGRESS      | Used to view the progress of restart apply or standby database apply, average speed, remaining time, etc. This view is important for analyzing standby database apply performance. |
| V$SYSTEM_EVENT           | Used to view statistics of various system wait events.      |
| V$SYSTEM_WAIT_CLASS      | Used to view statistics of various system wait classes.     |
| V$BUFFER_POOL_STATISTICS | Used to analyze the usage of the Data Buffer Pool.         |
| V$VMSTAT                 | Used to analyze the usage of the VM Pool.                  |
| V$SQL                    | Used to view the execution statistics of all current SQL statements. |
| V$GLOBAL_MPOOL           | Used to view statistics of instance-level appPool, sqlPool, and dcPool. |
| V$OPEN_CURSOR            | Used to view information related to each statement and the usage of appPool. |
| V$PLANCACHE              | Used to view the usage of the plan cache.                  |

For explanations of the specific meanings of all the fields in the above views, please refer to the reference manual [Dynamic Views](../../Reference Manual/System Views/Dynamic Performance Views/00Dynamic Performance Views).