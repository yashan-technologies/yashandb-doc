Before understanding tuning in practice, it is important to have a general understanding of the execution process of SQL statements, and to know how SQL statements are transformed step by step from the input string into the final execution result.

From the input of the SQL statement from the client to the output of the result to the client, the SQL engine processes through several steps including parsing, optimization, and execution.

## Parse & Verify Parsing and Verification

The first phase of SQL execution is parsing and verification. Parsing can be divided into soft parsing and hard parsing:

- Based on the HASH value of the client input SQL statement, the engine checks in the shared pool's plan buffer to see if a copy exists. If a copy exists, it reuses the query plan and directly jumps to the execution stage, which is called "soft parsing."

- If there is no copy in the shared pool's plan buffer, hard parsing is performed, where the SQL statement is tokenized to generate an abstract syntax tree that the system can recognize, and the syntax tree is verified:
   1. Verify the Table to check if it can be accessed normally and to verify privilege issues.
   2. Verify the Filter conditions, including checking if the column information in the filter conditions can be found in the table.
   3. Verify Columns to check each column node, verify whether the function is a built-in function or an externally declared function, as well as the filter conditions associated with each node.

>  **Note**: 
>
>  The SQL engine always performs hard parsing for DDL statements.

## Rewrite Statement Rewriting

After parsing and verification are completed, the SQL engine rewrites the abstract syntax tree into another equivalent logical description tree according to specific relational algebra rules, making it easier to optimize.

Rewriting is particularly important for very complex queries (including those with many subqueries or many joins). Query generator tools usually create these complex queries. YashanDB has various rules for statement rewriting, generally including the following categories:

- Subquery rewriting
- Predicate pushing
- Predicate combination optimization

For detailed information on rewriting and predicate-related optimization, see [Query Rewriting and Predicate Optimization](../Principle and Rules of SQL Tuning/Query Rewriting and Predicate Optimization).

## Optimize Optimization

In the Optimize phase, a logical plan is generated, and based on the referenced objects and conditions in the query, the most effective execution plan is determined after considering multiple factors, such as determining Join Orders and selectivity calculations. This plan is then converted into a physical plan according to relevant rules.

Generating an execution plan is a necessary step in processing any SQL statement and will greatly affect execution time.

## Execute Execution

The SQL engine uses a series of algorithms to organize and process data, typical ones being sorting, joining, table scanning, and aggregation, which are referred to as execution operators. The optimizer combines various operators like building blocks in the Optimize phase to ultimately generate the execution plan, and the executor then executes the operators step by step according to the plan to finally produce the data.