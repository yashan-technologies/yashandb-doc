YashanDB defines a series of PL statements for programming in specified formats. These statements are compiled by the built-in compiler of YashanDB to generate executable PL objects, such as stored procedures and functions, to fulfill various complex business logic at the database level.

## Expressions in PL

An expression in PL is a combination of one or more values, operators, and SQL functions, resulting in a single value. For more details, see [Expressions](../PL Language Fundamentals/Expressions).

## Static SQL Statements in PL

The following SQL statements can be written in PL:

- [DML](DML Statement): INSERT/UPDATE/SELECT/MERGE/DELETE, etc.
- [DCL](DCL Statement): COMMIT/ROLLBACK/SAVEPOINT, etc.

These SQL statements have the same syntax as ordinary SQL statements outside of procedure bodies, but with the following constraints:

- A SELECT statement must have an INTO clause, and the number of items in the select_list must correspond to the variables or RECORD members in the INTO clause.
- COMMIT/ROLLBACK can only use the default syntax for COMMIT/ROLLBACK and cannot specify any options.

## Dynamic SQL Statements in PL

Dynamic SQL statements can be executed in PL using the [EXECUTE Statement](EXECUTE Statement).

These SQL statements have the same syntax as ordinary SQL statements outside of procedure bodies, but with the following constraints:

- When dynamic SQL statements are non-anonymous block statements, each placeholder for a bind variable must correspond to a variable in the USING clause.
- When dynamic SQL statements are anonymous blocks, non-question mark placeholders with the same name will be treated as the same bind variable.
- For SELECT statements in dynamic SQL, if the query result is a single row, the SQL must include an INTO clause; if the query result is multiple rows, the SQL must include a BULK COLLECTION INTO clause.

## Control Statements in PL

- Loop control statements: [FOR loop statement](FOR Statement), [FORALL bulk loop statement](FORALL Statement), [WHILE loop statement](WHILE Statement), and [LOOP statement](LOOP Statement).
- Sequential control statements: [GOTO statement](GOTO Statement), [CONTINUE statement](CONTINUE Statement), and [EXIT statement](EXIT Statement).
- Conditional selection control statements: [IF statement](IF Statement) and [CASE statement](CASE Statement).
- Procedure body termination control statement: [RETURN statement](RETURN Statement).

## Other Statements in PL

- Data generation statement in pipe functions: [PIPE ROW statement](PIPE ROW Statement).