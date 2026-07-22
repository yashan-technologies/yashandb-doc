PL language is the procedural extension language of SQL for the YashanDB database. It is a portable, high-performance programming language. This document will introduce the advantages and main features of PL language.

## Advantages of PL

PL has the following advantages compared to other programming languages:

* SQL Integration

* Programmability Support

* High Performance

* Portability

### SQL Integration

The integration of PL language and Structured Query Language (SQL) is very tight, which is reflected in the following aspects:

* PL language allows static SQL operations, which means direct use of all DQL, DML data operations, transaction control statements, as well as all built-in functions, advanced package functions, operators, and pseudo-columns.
* PL language allows all SQL operations through dynamic SQL.
* PL language fully supports all data types defined in SQL.
* PL language supports cursor variables, providing flexible cursor operations such as OPEN, FETCH, CLOSE, assignment, input/output parameters, and return to the client.
* In PL language, you can specify the data type for variables based on the column or row data types of database tables without explicitly specifying the data type.
* When running DQL queries in PL language, one row of the result set will be processed at a time.
* PL language can locate UDFs, which can be used directly in DQL statements.
* Errors caused by operations in PL can be caught through the exception handling module.

### Programmability Support

The combination of SQL and databases is very tight, mainly used to describe how to access and process databases, including create, read, update, and delete operations. However, SQL's programmability is not strong, and PL language is an extension addressing this weakness.

* PL language supports variable declaration, assignment, default expressions, actual parameters and formal parameters.

* Variables in PL language, apart from ordinary data type scalar forms, also support vector forms such as arrays, OBJECTs, NEST TABLEs defined by user-defined types (UDTs).

* PL language provides control structures such as loops, conditions, jumps, and empty lines.

* PL language offers an exception handling module.

* PL language provides various database object types, including stored procedures, UDFs, triggers, and anonymous blocks, offering different usage scenarios.

### High Performance

PL language, through programmable logic and SQL integration, can implement application logic directly in the database, significantly reducing the interaction traffic between applications and the database.

* By binding variables, when using DML/DQL operations in PL language, the PL compiler converts PL variables appearing in SQL statements into bound variables.

* YashanDB database reuses the SQL statement compilation buffer every time the same SQL statement is executed, thus improving performance.

* When using dynamic SQL in PL language, bound variables are not automatically created, but can be explicitly specified through the USING clause in the EXECUTE IMMEDIATE statement.

* PL programs are compiled upon first call; once compiled, they enter the PL POOL buffer, allowing for subsequent reuse and improved performance.

### Portability

PL language is a portable standard language for database development, usable on any operating system and platform that runs the database.

## Main Features of PL

PL language combines the data manipulation capabilities of SQL with the processing capabilities of procedural languages, mainly including the following features:

* Statement Blocks

* Variables and Constants

* PL Data Types

* Rich Statement Forms

* Diverse Object Forms

* Exception Handling Module

### Statement Blocks

The basic unit of PL language is the statement block, which groups related declarations and statements.

Statement blocks can be categorized by different keywords, specifically defined as follows:

1. DECLARE: Declaration section.
2. BEGIN - END: Executable section (mandatory).
3. EXCEPTION: Exception handling section.

In YashanDB, statement blocks support obtaining aliases using labels, which makes these blocks have a one-time use feature and also supports nesting.

### Variables and Constants

The declaration section of a PL statement block allows for the declaration of variables and constants, which can then be used anywhere expressions are allowed.

The difference between variables and constants lies in that the value of a variable can change during program execution, while the value of a constant cannot.

In cases where multiple statement blocks are nested, if there is a variable name conflict, the default behavior will prioritize the variable declared in the innermost block.

PL supports two types of variable references:

- PL can treat declared variables as a RECORD using %ROWTYPE, with the members of that RECORD stemming from the members of the object referenced by %ROWTYPE.
- PL can align the data type of a variable with that of another variable or column field using %TYPE.

### PL Data Types

PL supports the use of all SQL data types for declarations, which can be declared as ordinary scalars or as vectors like arrays, OBJECTs, and NEST TABLEs. It also supports enhancements via UDTs and type cascading nesting.

### Rich Statement Forms

* PL language's control statements provide the following capabilities: conditional selection statements that can run different statements based on different data values; loop statements that can repeat the same statement for a series of different data values; sequential control statements that allow jumps to specified labeled statements.

* PL provides static SQL, dynamic SQL, and implicit cursor attribute capabilities.

* PL includes cursor-related statements and cursor attribute capabilities.

* PL supports direct invocation of expressions, operators, built-in functions, built-in advanced packages, and pseudo-columns.

* PL allows for the declaration and usage of system and user-defined exceptions.

### Diverse Object Forms

* An anonymous block execution is simply executing a PL statement block.

* It supports defining PROCEDUREs, which are executed by PL statement blocks, not directly returning results, but can provide input/output parameters.

* It supports defining UDFs, including PL language UDFs, external JAVA UDFs, and external C UDFs. UDFs can be called directly by SQL language or executed by PL statement blocks, returning results directly.

* It supports defining TRIGGERs, which currently offer statement-level or row-level triggering before and after DML statement execution.

* It supports defining packages, which are schema objects that group logically related PL types, variables, constants, stored procedures, UDFs, cursors, and exceptions. Packages are stored in the database and can be accessed directly. The database itself also provides many advanced packages; please refer to the [Built-in Advanced PL Packages](./Built-in Advanced PL Packages/00Built-in Advanced PL Packages) chapter for details.

### Exception Handling Module

The exception handling module of PL is mainly used to detect and handle errors.

When an error occurs during an SQL operation, it will simultaneously cause an exception in PL, at which point the exception handling module will detect the error.

## Usage Constraints

When using distributed in-memory databases, due to the need for strict partition management of data and business, PL object DDL operations must be executed on CN, and PL object DML execution operations must be executed on DN to ensure data consistency. In actual business applications, DDL execution operations can be directly connected to DN by configuring connection strings at the application end, or can be configured with CN connection strings and automatically distributed by CN through [JDBC driver support for data sharding](../JDBC Driver/YashanDB JDBC User Guide/Support for Data Sharding with In Memory Database).

The PL operations allowed to be executed on CN nodes of distributed in-memory database include `CREATE/ALTER/DROP PROCEDURE`. 

Only `EXECUTE PROCEDURE` PL operation is allowed to be executed on DN nodes of distributed in-memory database.


