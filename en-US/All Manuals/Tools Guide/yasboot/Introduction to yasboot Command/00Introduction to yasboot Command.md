Command Format
----

```shell
yasboot {SUBCOMMAND1} {SUBCOMMAND2} {OPTIONS}
```

The first-level command indicates the module support provided by *yasboot*, while the second-level command indicates the operation.

SUBCOMMAND1: 

|Option |Meaning |
| ------------ | ------------------------------------------ |
| *cluster*     | Database cluster management command         |
| *collection*  | One-click collection command                |
| *config*      | Database cluster configuration management command |
| *dataspace*   | Database redistribution management command   |
| *discovery*   | Database AC discovery command               |
| *election*    | Configuration and management of leader election based on yasom |
| *group*       | Management of node groups                   |
| *host*        | View the running status of the server      |
| *init*        | Quick deployment command for standalone database |
| *ipchange*    | Command to change server IP in the database cluster |
| *job*         | Schedule management command                 |
| *load*        | One-click split and import CSV data        |
| *monit*       | Monit management command                    |
| *node*        | Management of a single node                 |
| *nvme*        | Manual reconstruction of the NVMe disk configuration of the distributed cluster               |
| *package*     | Database cluster configuration and deployment command |
| *patrol*      | Database inspection management              |
| *process*     | Management of yasom, yasagent, yasdb processes |
| *sql*         | Specify to execute yasql on a certain node  |
| *task*        | Manage tasks executed by *yasboot*         |
| *whitelist*   | Whitelist management command                |
| *ycs*         | YAC/Distributed Cluster YCS management command       |
| *yfs*         | YAC/Distributed Cluster YFS management command       |