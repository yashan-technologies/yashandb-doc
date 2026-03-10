- [Job Management](Job Management) refers to executing SQL tasks on a scheduled basis using the *yasboot* tool, for example, the scheduling of DBMS_STATS-related tasks to manage the optimizer's statistics. The applicability of the schedule and product deployment format depends on the actual SQL tasks that need to be executed.

- [Session Management](Session Management) mainly explains how to switch session modes and configure related parameters.

- To enhance security, when executing the following functionality, YashanDB will autonomously start the [yex_server sandbox process](yex_server Sandbox Process Management) to independently load the corresponding functionality modules.

  - Calling external stored procedures (such as external UDFs).

  - Performing INSERT, DELETE, UPDATE, and SELECT operations on remote tables via dblink.