YashanDB optimizer contains many optimization details. Among them, the following optimization principles and rules should be understood before performing SQL tuning:

- Query rewriting and predicate optimization
- Selectivity and statistics

Typically, the optimizer can make optimal choices. However, if it does not generate an optimal plan due to reasons such as the statement itself, SQL-level tuning is necessary. Common SQL tuning methods include:

- **Rewrite SQL**

    For beginners, SQL can be simply understood as a messaging language—text queries and data returns. However, without a deep understanding and application of this language, clients may often produce inefficient SQL.

    Developers write SQL statements to request results from the database, and the database responds and executes SQL. For two differently written SQL statements that produce the same result, their response times may differ by several orders of magnitude.

    When performance issues arise during SQL execution, tuners should first check if the statement is written reasonably, such as whether binding variables are not used and constants are directly used (which can lead to the SQL execution plan being non-reusable, resulting in resource-consuming hard parsing). Improving performance through SQL rewriting is an important aspect of database performance tuning.

- **Hint Tuning**

    When SQL has been rewritten to meet requirements, but the execution efficiency is still unsatisfactory, it may be necessary to intervene in the optimizer to generate the expected execution plan.

    In YashanDB, users can change the optimizer's plan selection by specifying [Hint](../SQL Tuning Tools/Hint). However, in CBO mode, the optimizer may sometimes ignore hints it deems unreasonable. In such cases, analysis should be conducted based on knowledge of the optimizer's workings, and hints should be adjusted.

    Statistics are the benchmark for the optimizer's work. For user tuning, understanding the data information, business logic, and relationships of the tables involved in the SQL statement is fundamental for making reasonable hint choices.

- **Plan Recording**

    After tuning the plan using hints, it can be recorded via [Outline](../SQL Tuning Tools/Outline). When encountering the same execution statement next time, a stable execution plan can be directly retrieved.