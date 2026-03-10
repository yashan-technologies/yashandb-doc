In the declaration section of a PL block, subprograms can be defined.

Nested subprograms can be procedure bodies or functions, and can be defined in the declaration sections of anonymous blocks, stored procedures, UDFs, packages, and other PL objects.

Nested subprograms can define additional nested subprograms inside them.

Defining Nested Subprograms
------

The syntax for defining a procedure is:

```ebnf+diagram
syntax::= PROCEDURE procedure_name ["(" (argument_define) {"," (argument_define)} ")"] (IS|AS) procedure_body_clause ";"
```

**procedure_body_clause::=**

```ebnf+diagram
syntax::= [variable_declare] BEGIN pl_statements END [procedure_name]
```

The syntax for defining a function is:

```ebnf+diagram
syntax::= FUNCTION function_name ["("  (argument_define) {"," (argument_define)}  ")"]  RETURN return_datatype (IS|AS) function_body_clause ";"
```

**function_body_clause::=**

```ebnf+diagram
syntax::= [variable_declare] BEGIN pl_statements END [function_name]
```

Rules and Constraints for Nested Subprograms:

- Nested subprograms do not support separation of declaration and definition.
- Nested subprograms do not support the ACCESSIBLE BY/DEFAULT COLLATION/AUTHID attributes.
- Nested subprograms do not support overloading.
- The lifecycle of nested subprograms is limited to the scope of the defining procedure body.
- If a nested subprogram is defined in a standalone stored procedure/function or in a stored procedure/function within a higher-level package or UDT, it will be persisted to the database together.
- When multiple blocks are nested, if a name conflict occurs for nested subprograms, the subprogram defined in the most recent block takes precedence.
- The definition of nested subprograms must be placed at the end of the declaration section.

Calling Nested Subprograms
------

The format for calling a procedure is:

_procedure_name(arguments);_

The arguments are actual parameters corresponding to argument_define.

The format for calling a function is:

_variable_name := procedure_name(arguments);_

***Example***1

```plsql
DECLARE
    VAR1 INT;
    PROCEDURE YA_NT_PROC(A1 INT, A2 INT) IS
    BEGIN
         VAR1 := A1 + A2;
    END;
BEGIN
   YA_NT_PROC(1,2);
   DBMS_OUTPUT.PUT_LINE(VAR1);
END;
/

--result
3
```

***Example***2 for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE PROCEDURE YA_PROC1 IS
    SUM1 INT;
    FUNCTION YA_NT_FUN(A1 INT, A2 INT) RETURN INT IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE(A1);
        DBMS_OUTPUT.PUT_LINE(A2);
        RETURN A1 + A2;
    END;
BEGIN
   SUM1 := YA_NT_FUN(7, 9);
   DBMS_OUTPUT.PUT_LINE(SUM1);
END;
/

BEGIN
    YA_PROC1();
END;
/

--result
7
9
16
```
