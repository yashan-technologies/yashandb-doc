General Description
----

The ALTER PROCEDURE statement is used to explicitly recompile a stored procedure.

For stored procedures in the SYS schema, the ALTER PROCEDURE statement must be executed by the SYS user.

For stored procedures in other schemas, the ALTER PROCEDURE statement must be executed by the owning user or a user with the ALTER ANY PROCEDURE privilege.

For meanings and limitations of stored procedures, refer to the [Stored Procedures](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/Stored Procedures) section.

Statement Definition
----

**alter procedure::=**

```ebnf+diagram
syntax::= ALTER PROCEDURE [schema "."] procedure_name (procedure_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

The name of the schema that contains the stored procedure. If omitted, it defaults to the schema of the currently logged-in user.

### 2. procedure\_name

The name of the stored procedure that needs to be explicitly recompiled.

### 3. procedure\_compile\_clause

Specifies the recompilation options; refer to the [compile_clause](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/compile_clause) description for details.

### 4. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no actual meaning.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER PROCEDURE sales.ya_proc COMPILE;
```
