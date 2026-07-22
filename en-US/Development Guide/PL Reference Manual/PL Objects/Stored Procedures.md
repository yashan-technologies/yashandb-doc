Stored procedures are a type of PL object in the database.

- In Standalone Primary-Standby or Primary-Standby Cluster Deployment, stored procedures can only be created, managed, and used on the primary database/primary cluster.

- Stored procedures are not suitable for ISC Distributed Cluster Deployment.

Creating a Stored Procedure
------

Use the [CREATE PROCEDURE](../../SQL Reference Manual/SQL Statements/CREATE PROCEDURE) statement to create a stored procedure, with the syntax defined as follows:

**create procedure::=**

```ebnf
= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] PROCEDURE procedure_head_clause (IS|AS) procedure_body_clause ";".
```

**procedure_head_clause::=**

```ebnf
= [schema "."] procedure_name ["(" argument_define {"," argument_define} ")"] [invoker_rights_clause].
```

**invoker_rights_clause::=**

```ebnf
= AUTHID (CURRENT_USER|DEFINER).
```

<span id="procedurebodyclause" name="procedurebodyclause"></span>

**procedure_body_clause::=**

```ebnf
= [variable_declare] BEGIN pl_statements END [procedure_name].
```

### OR REPLACE

Rebuild the stored procedure if it already exists.

### EDITIONABLE | NONEDITIONABLE

These are for syntax compatibility and have no practical meaning.

### procedure\_name

The name of the stored procedure, which is mandatory and must comply with YashanDB's [object naming rules](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### argument\_define

Defines the parameters (formal parameters) of the stored procedure, which can be omitted, in which case procedure_name()=procedure_name. Up to 4095 parameters can be specified.

For more details, please refer to [PL Parameter Description](../Parameters/00Parameters).

### invoker_rights_clause

For more details, please refer to the description related to [invoker_rights_clause](00PL Objects.md#invoker).

### variable\_declare

Declares global variables for the PL procedure body, which can be omitted.

For more details, please refer to [PL Declaration](../PL Language Fundamentals/Variables/00Variables).

### pl\_statements

Defines the execution statements within the procedure body, which can be one or more items from [PL Statements](../PL Statements/00PL Statements).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(i INT) IS
BEGIN
CASE i
WHEN 1 THEN
DBMS_OUTPUT.PUT_LINE('hello');
WHEN 2 THEN
DBMS_OUTPUT.PUT_LINE('world');
END CASE;
END ya_proc;
/
```

Running a Stored Procedure
------

### Calling in SQL Statement

Call the stored procedure using the CALL or EXEC statement, with the syntax defined as:

```ebnf
= (CALL|EXEC) procedure_name ["(" arguments ")"].
```

Arguments are the actual parameters corresponding to argument_define.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec ya_proc(1);

--result
hello
```

### Calling within Procedure Body

The format is: _procedure_name(arguments);_.

Arguments are the actual parameters corresponding to argument_define.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
  ya_proc(1);
  ya_proc(2);
END;
/

--result
hello
world
```

Deleting a Stored Procedure
------

Use the [DROP PROCEDURE](../../SQL Reference Manual/SQL Statements/DROP PROCEDURE) statement to delete a stored procedure.

Modifying a Stored Procedure
------

Use the [ALTER PROCEDURE](../../SQL Reference Manual/SQL Statements/ALTER PROCEDURE) statement to modify a stored procedure.