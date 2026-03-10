The SQL engine is one of the core components of the database. Its main responsibilities are to process SQL requests submitted in text form by clients and execute them, as well as to return the query result set to clients when necessary.

## SQL Execution Process

A complete SQL execution process consists of four stages: parsing, verification, optimization, and execution. The optimization stage is further divided into REWRITE, execution plan generation, and dynamic rewriting.

![](./image/sqlflow.png)

- **Parse Stage**

    In the parse stage, lexical, syntactical, and semantic parsing are performed, generating a tree-like parsing result called the Parse Tree.

- **Verify Stage**

    In the verify stage, user role privilege verification, data validity checks, and syntactic constraint validations are conducted. Additionally, part of the work of the optimizer is brought forward to optimize the Parse Tree structure, thereby reducing the burden on subsequent stages for performance acceleration.

- **Optimize Stage**

    In the optimize stage, the final executable SQL execution plan is generated based on the input Parse Tree.

- **Execute Stage**

    In the execute stage, operators in the SQL execution plan are executed, supporting parallel computation to improve efficiency.

## Optimizer

The optimizer is a core component of the SQL engine. The YashanDB optimizer employs a Cost Based Optimizer (CBO) mode.

The optimizer generates the optimal execution plan as much as possible based on the input Parse Tree and provides it as input for the executor to complete the subsequent execution process. The execution plan includes data access paths, table join order, and other execution operator information. The CBO optimizer of YashanDB calculates the cost required for data access and processing based on statistics, selecting the best scheme to generate the execution plan.

- **Statistics**

    This mainly includes statistics of tables, columns, and indexes, such as the number of rows in a table, the average length of the columns, and the number of columns in the index. Statistics can be collected dynamically, through scheduled tasks, and manually triggered methods. Meanwhile, technologies like parallel statistics and sampling statistics are used to accelerate the efficiency of statistics collection, providing timely updated information for the optimizer.

- **Execution Operators**

    Operators define specific types of computational operations and are the basic building blocks of the execution plan. YashanDB implements the following basic operators:

    - Scan Operator

    - Join Operator

    - Query Operator

    - Sort Operator

    - Auxiliary Functionality Operator

    - PX Parallel Execution Operator

- **HINT**

    HINT provides measures for users to intervene in the SQL execution plan, such as specifying the table scan method, execution order, and parallel degree. The optimizer will generate the optimal execution plan based on these hints in conjunction with the statistics.

- **Parallelism**

    Parallelism describes the level of concurrent processing during SQL execution. The parallel degree can be specified through parameters or HINTs, allowing SQL to be executed using multi-threaded concurrent execution, thereby improving the execution efficiency of SQL statements.

## Vectorization Calculation

YashanDB supports vectorization calculation, where the core principle is to utilize SIMD (Single Instruction Multiple Data) technology for batch computations, improving calculation efficiency.

The content of vectorization calculation includes:

- Batch processing: Data transmitted between operators is no longer individual records but a batch of data.

- Parallel computation: Operators are executed concurrently.

The vectorization calculation framework includes:

- Vectors: The data structure used for transmitting data between operators, consisting of a batch of contiguous memory that stores column data of the same type and known length.

- Expressions: General expressions such as literals, columns, and functions. By establishing a computational expression structure and binding it to the required context information and schema, an executable expression is created for computation.

- Execution Operators: Operators are functionality units in SQL that execute the query plan. They process input vector data and output results, which are also vector data.

## Distributed SQL Execution Process

In a distributed SQL execution process, there are mainly two types of instances involved:

- CN (Coordinating Node): Responsible for providing interfaces to the outside, receiving user requests, generating distributed execution plans, and distributing query plans to DN for execution and summarizing execution results.

- Data Instance (DN): Responsible for storing data and executing the distribution plans issued by CN in parallel.

The distributed SQL engine parses, verifies, optimizes the user's textual SQL statements, distributes execution plans from CN to DN, performs concurrent execution on multiple nodes of CN and DN, and finally returns the query result set to the user.

![](./image/dst-sql.png)

- **session**

    Session management is used for managing the interaction between nodes, overseeing the final status of execution between nodes, and scheduling the execution process among nodes.

- **Distributed Interconnect Network**

    This distributed communication component uses an asynchronous network communication framework responsible for network communication between nodes, including the execution plans dispatched from CN to DN and data exchange between nodes.

- **Data Exchange Mechanism**

    In a ISC distributed database, data shards are stored on different nodes. When a SQL computation requires data sources from different shards, a specific PX parallel execution operator is needed to transport data to a specified location in the designated manner.

    During a distributed SQL execution process, the following types of data exchanges may occur:

    - Data from DN aggregates to CN as query results of the distributed SQL.

    - CN sends data to insert or update to DN.

    - A computation on a certain DN requires data from another DN, necessitating the transportation of that data.
    
    - Data is exchanged between stages on the same node.

- **Parallel Execution**

    YashanDB's distributed SQL execution employs a typical MPP architecture, divided into two levels:

    - **First Level: Inter-node Parallelism**

        The optimizer on CN divides a complex query into multiple stages based on table data distribution information and sends them to different DN, allowing for parallel execution between DN/stages.

    - **Second Level: Intra-node Parallelism**

        Intra-node parallel execution can be divided into two types:

        - Horizontal Partitioning: Stages generated by the optimizer on CN can be placed into multiple pipelines on DN based on shard data information, with each pipeline processing a range of data.

        - Vertical Partitioning: If resource remains after horizontal partitioning, additional vertical partitioning can be performed to divide the stages into smaller stages for parallel execution, fully utilizing CPU multi-core capabilities.