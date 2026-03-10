An anonymous block is a special type of PL object in the database. Unlike other objects, it is not persisted, runs immediately after creation, and cannot be called.

Creating Anonymous Blocks
-----

Anonymous blocks have no name or parameter definitions, and only contain the executable body. Its syntax is defined as:

```ebnf+diagram
syntax::= [DECLARE variable_declare] BEGIN pl_statements END ";"
```

### 1. variable\_declare

Declares global variables for the PL body. This is optional.

[PL Declaration](../PL Language Fundamentals/Variables/00Variables).

### 2. pl\_statements

Defines the execution statements within the body. This consists of one or more items from [PL Statements](../PL Statements/00PL Statements).

***Example***

```plsql
DECLARE
i INT :=1;
BEGIN
CASE i
WHEN 1 THEN
DBMS_OUTPUT.PUT_LINE('hello');
WHEN 2 THEN
DBMS_OUTPUT.PUT_LINE('world');
END CASE;
END;
/
```
