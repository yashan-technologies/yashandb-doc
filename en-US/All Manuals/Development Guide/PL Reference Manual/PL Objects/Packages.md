Package is a type of PL object in the database.

Package is not suitable for ISC Distributed Cluster Deployment.

## Creating a Package

The creation of a package consists of two parts:

- Defining PACKAGE HEAD: The PACKAGE HEAD is used to declare public (PUBLIC attribute) variables, public types, public cursors, and public subprogram objects (stored procedures, UDF). Once the package is successfully created, public variables, public types, public cursors, and public subprogram objects can be referenced as members of the package by other external programs.
- Defining PACKAGE BODY: The PACKAGE BODY is used to define the body of procedures and private (PRIVATE attribute) variables. The PACKAGE BODY can also directly use public variables. Private variables can only be used within the PACKAGE BODY and cannot be referenced by other external subprograms.

PACKAGE HEAD and PACKAGE BODY are defined with different syntax, and YashanDB does not strictly require the creation order of HEAD and BODY. For example, if BODY is defined before HEAD is created, the system will still create the package, but it will throw a compilation error because the HEAD declaration does not exist, and the package cannot be executed or called until the HEAD is created.

The system also imposes the following constraints on PACKAGE HEAD and PACKAGE BODY:

- No variables or subprogram object declarations with the same name are allowed inside HEAD and BODY;
- For subprogram object declarations in HEAD, there must be corresponding body definitions in BODY. The default values for parameters declared in HEAD may be omitted in BODY;
- For subprogram objects, HEAD can only declare them, and BODY can only define their body.

You can create the PACKAGE HEAD or PACKAGE BODY using the [CREATE PACKAGE](../../SQL Reference Manual/SQL Statements/CREATE PACKAGE) statement, with the following syntax definition:

**create package::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] PACKAGE (package_head_clause|package_body_clause) END [package_name]";"
```

**package_head_clause::=**

```ebnf+diagram
syntax::= [schema "."] package_name [invoker_rights_clause] (IS|AS) [pragma_clause] (package_item_clause) {"," (package_item_clause)}
```

**invoker_rights_clause::=**

```ebnf+diagram
syntax::= AUTHID (CURRENT_USER|DEFINER)
```

**pragma_clause::=**

```ebnf+diagram
syntax::= PRAGMA SERIALLY_REUSABLE";"
```

**package_item_clause::=**

```ebnf+diagram
syntax::= (variable_declare|pl_declare|func_declare)
```

[variable_declare](../PL Language Fundamentals/Variables/00Variables)

**pl_declare::=**

```ebnf+diagram
syntax::= PROCEDURE pl_name ["(" (argument_define) {"," (argument_define)} ")"] [RETURN return_datatype]
```

**func_declare::=**

```ebnf+diagram
syntax::= FUNCTION func_name ["(" (argument_define) {"," (argument_define)} ")"] RETURN return_datatype [{PIPELINED|PARALLEL_ENABLE|DETERMINISTIC}]
```

To create a pipelined function within a package, the PIPELINED option must be specified, and the usage rules for the pipelined function are similar to those of [standalone pipelined functions](User-Defined Functions.html#pipelined). 

The PARALLEL_ENABLE syntax usage rules are the same as those for [PARALLEL_ENABLE](User-Defined Functions.html#parallel_enable).

The DETERMINISTIC syntax usage rules are the same as those for [DETERMINISTIC](User-Defined Functions.html#deterministic).

**package_body_clause::=**

```ebnf+diagram
syntax::= BODY [schema "."] package_name (IS|AS) [package_item_clause] package_subprogram_clause
```

**package_subprogram_clause::=**

```ebnf+diagram
syntax::= (IS|AS) [variable_declare] BEGIN plsql_statements END [pl_name]
```

### 1. **OR REPLACE**

When the package to be created already exists, it will be rebuilt.

### 2. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no actual meaning.

### 3. package\_name

The name of the package, which cannot be omitted and must conform to YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 4. package\_head\_clause

Creates a HEAD for a package.

#### 4.1. invoker\_rights\_clause

For details, please refer to the description of [invoker_rights_clause](00PL Objects.html#invoker).

#### 4.2. pragma\_clause

Optional, indicating whether the package variables are reset to the default values defined at the time of declaration each time they are referenced. By default, they are not reset.

In YashanDB, variables declared in the package are session-level global variables, meaning that in the same session, variable assignments affect each other, while variables in different sessions are independent. By setting SERIALLY_REUSABLE, the variable can regain its initial value upon each reference (from client to server).

***Example*** for Standalone Deployment and YAC Deployment

```plsql
DROP PACKAGE IF EXISTS calc_fee;

CREATE OR REPLACE PACKAGE calc_fee
AS
c NUMBER := 100;
PROCEDURE branch_quantity(date_from date);
END;
/

CREATE OR REPLACE PACKAGE BODY calc_fee AS
PROCEDURE branch_quantity(date_from DATE) IS
BEGIN
 DBMS_OUTPUT.PUT_LINE(c);
 c := c+1;
 DBMS_OUTPUT.PUT_LINE(c);
END;
END calc_fee;
/

-- First execution
exec calc_fee.branch_quantity(sysdate);
100
101
-- Second execution
exec calc_fee.branch_quantity(sysdate);
101
102

-- Setting pragma_clause
CREATE OR REPLACE PACKAGE calc_fee
AS
PRAGMA SERIALLY_REUSABLE;
c NUMBER := 100;
PROCEDURE branch_quantity(date_from date);
END;
/

CREATE OR REPLACE PACKAGE BODY calc_fee AS
PRAGMA SERIALLY_REUSABLE;
PROCEDURE branch_quantity(date_from date) IS
BEGIN
DBMS_OUTPUT.PUT_LINE(c);
c := c+1;
DBMS_OUTPUT.PUT_LINE(c);
END;
END calc_fee;
/

-- First execution
exec calc_fee.branch_quantity(sysdate);
100
101
-- Second execution
exec calc_fee.branch_quantity(sysdate);
100
101
```

#### 4.3. package\_item\_clause

Member declarations of the package, including variable declarations and subprogram object declarations. The maximum number of members that can be declared in a package (HEAD or BODY) is 1024.

- Variable declarations: Supports the types of variable declarations listed in the [Variables](../PL Language Fundamentals/Variables/00Variables) section. However, for cursors, explicit cursor definitions are allowed, but explicit cursor declarations are not allowed. Multiple different types of variables can be declared simultaneously, and the order is unrestricted.
- Subprogram object declarations: Supports the declaration of stored procedures and UDF. Multiple stored procedures and UDFs can be declared simultaneously, and the order is unrestricted.

Variable declarations must be placed before subprogram object declarations, and the order of defined members determines whether the latter member can see the former.

### 5. package\_body\_clause

Creates a BODY for a package.

#### 5.1. package\_subprogram\_clause

Defines the subprogram bodies in the package, including the definitions of PROCEDURE or FUNCTION. The definition syntax is the same as that in the [Stored Procedures](Stored Procedures) and [UDF](User-Defined Functions) sections.

## Loading Package Variables

Package variables cannot be directly accessed in external SQL statements but must be loaded and used within subprograms (internal or external) in the following format:

*\[schema.\]\[package_name.\]variable_name;*

When called by external subprograms, the package_name cannot be omitted.

In YashanDB, the variables of the package are managed in binding with the package, meaning that if the HEAD or BODY of the package changes, all variables of that package will be reset. This rule differs from Oracle databases.

## Running Subprogram Objects of Package

**Calling Sub Function in SQL Statement**

Sub functions of the package can be invoked within SQL statements in various positions such as column items, condition items, or query options (like ORDER BY/LIMIT/OFFSET). For example, the calling format that appears in the query column is:

*SELECT package_name.subpl_name[(arguments)] FROM table_name;*

**Calling Sub Stored Procedures using CALL/EXEC Statements**

Sub stored procedures of the package can be called using CALL/EXEC statements, with the calling format:

 *(CALL|EXEC) package_name.subpl_name[(arguments)];*

**Calling Sub Stored Procedures within the Body of Procedures**

Sub stored procedures can be called within the body (internal or external) using the format:

*[package_name.]procedure_name[(arguments)];*

When called by external subprograms, the package_name cannot be omitted.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Creating package without HEAD
DROP PACKAGE IF EXISTS calc_fee;

-- Creating BODY of package, it will return an error as related variables are not declared, but the BODY is already successfully created
CREATE OR REPLACE PACKAGE BODY calc_fee AS
PROCEDURE calc_rev(prefix CHAR) IS
 str1 VARCHAR(100) := 'select sum(revenue_total) from finance_info where substr(branch,0,2)=:a';
BEGIN
 EXECUTE IMMEDIATE str1 INTO c USING prefix;
 DBMS_OUTPUT.PUT_LINE(c);
END;
END calc_fee;
/
-- result
YAS-05278 cannot compile body without its specification

-- Creating HEAD for calc_fee (this step will not require to create BODY again)
CREATE OR REPLACE PACKAGE calc_fee
AS
c NUMBER := 100;
PROCEDURE calc_rev(prefix CHAR);
END calc_fee;
/

-- Calling a package
DECLARE
 c CHAR(2);
BEGIN
 c := '01';
 DBMS_OUTPUT.PUT_LINE(c);
 DBMS_OUTPUT.PUT_LINE(calc_fee.c);
 calc_fee.calc_rev(c);
END;
/
-- result
01
100
76666

-- Creating pipelined function in the package 
CREATE OR REPLACE PACKAGE ya_pkg1 AUTHID DEFINER AS
  TYPE NUMSET_T IS TABLE OF NUMBER;
  FUNCTION ya_pkg_pipe_func(p1 number) RETURN NUMSET_T PIPELINED;
END ya_pkg1;
/

CREATE OR REPLACE PACKAGE BODY ya_pkg1 AS
  FUNCTION ya_pkg_pipe_func(p1 NUMBER) RETURN NUMSET_T PIPELINED IS
  BEGIN
    FOR i IN 1..p1 LOOP
      pipe row(i);
    END LOOP;
    RETURN;
  END ya_pkg_pipe_func;
END ya_pkg1;
/
```

<span id="overload" name="overload" class="yaslink"></span>

## Overloading Subprogram Calls

Advanced packages allow defining multiple subprograms with the same name but different [parameter](../Parameters/Formal and Actual Parameters) names, counts, orders, or [data types](../../SQL Reference Manual/Data Types/00Data Types) to be used. PL can filter calls to such identically named subprograms through the overloading functionality.

The constraints for PL subprogram overloading are as follows:

- PL overload target objects must be subprograms inside packages, and cannot be global/nested subprograms.

- The following subprograms cannot serve as PL overload target objects:
  
  - Different subprograms differing only in the IN and OUT modes of their parameters.

  - Different subprograms differing only by having or not having default parameter values.
  
  - Different functions differing only in return value types.

If multiple identically named subprograms are defined in a package, when calling the subprogram with the corresponding name, YashanDB will match the passed [actual parameters](../Parameters/Formal and Actual Parameters) with the parameter lists of each subprogram to determine which one will actually be executed.

The matching dimensions include parameter names, counts, orders, and data types. The matching rules for data types are as follows:

- If only types are inconsistent and all are [numeric types](../../SQL Reference Manual/Data Types/Numeric Types), it will match according to the closest type and precision first, selecting the first matched subprogram for the call.

- If only types are inconsistent, YashanDB will first attempt to implicitly convert the actual parameters' data types [to match](../../SQL Reference Manual/Data Types/Data Type Conversion) the parameter types.

The calling method for overloaded subprograms is flexible. To avoid exceptions or unexpected results during the implicit conversion, it is recommended to:

- Clearly define the types of data that each parameter of the subprogram accepts, and use data types that completely match the parameter types when calling.

- Use more precise data type expressions, such as passing FLOAT typed data as 3.0f.

- Use type conversion functions to convert actual parameters to match the data types of the parameters in the target subprogram, such as using the [CAST](../../SQL Reference Manual/Built-in Functions/CAST) function or [explicit conversions](../../SQL Reference Manual/Data Types/Data Type Conversion).

***Example*** for Standalone Deployment and YAC Deployment

```plsql
DROP PACKAGE IF EXISTS pack_calc;
CREATE OR REPLACE PACKAGE pack_calc AS
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER) RETURN NUMBER;
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER, val3 NUMBER) RETURN NUMBER;
END;
/

CREATE OR REPLACE PACKAGE BODY pack_calc AS
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER) RETURN NUMBER IS 
    BEGIN
        IF val1 > val2 THEN
            RETURN val1;
        ELSE
            RETURN val2;
        END IF;
    END;
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER, val3 NUMBER) RETURN NUMBER IS 
    BEGIN
        RETURN calc_max(calc_max(val1, val2), val3);
    END;
END;
/

SELECT pack_calc.calc_max(1, 2) AS a, pack_calc.calc_max(1, 2, 3) AS b FROM dual;

-- result
          A           B
----------- -----------
          2           3

```

When calling overloaded subprograms, if the number or types of parameters do not correctly match any subprograms, error YAS-06923 will be reported. If during the subprogram call, multiple subprograms that meet the conditions are matched, error YAS-06924 will be reported. Common error scenarios are described below:

- When there are identically named subprograms at the time of declaration

  Example (Standalone, YAC Deployment)

  ```plsql
  CREATE OR REPLACE PACKAGE pack1 AS
      FUNCTION func(val NUMBER) RETURN NUMBER;
      FUNCTION func(val NUMBER) RETURN NUMBER;
  END;
  /
  YAS-05290 duplicate item "FUNC" in package

  -- Using different number of parameters, names, or data types can pass validation
  CREATE OR REPLACE PACKAGE pack2 AS
      FUNCTION func(val NUMBER) RETURN NUMBER;
      FUNCTION func(val DOUBLE) RETURN NUMBER;
  END;
  /
  ```
  
- Different subprograms are declared, but ambiguity arises during the call

***Example*** for Standalone Deployment and YAC Deployment

  ```plsql
  CREATE OR REPLACE PACKAGE pack1 AS
      FUNCTION func(val1 NUMBER) RETURN NUMBER;
      FUNCTION func(val1 NUMBER, val2 NUMBER default 3) RETURN NUMBER;
  END;
  /

  CREATE OR REPLACE PACKAGE BODY pack1 AS
      FUNCTION func(val1 NUMBER) RETURN NUMBER IS BEGIN RETURN 1; END;
      FUNCTION func(val1 NUMBER, val2 NUMBER default 3) RETURN NUMBER IS BEGIN RETURN 2; END;
  END;
  /

  SELECT pack1.func(val1 => 3) FROM dual;
  -- The system cannot match to a unique function leading to an error
  YAS-06924 too many declarations of 'FUNC' match this call

  SELECT pack1.func(val1 => 3, val2 => 4) FROM dual;
  -- The parameter name allows for identification of the invoked function, execution is successful
  PACK1.FUNC(VAL1=>3,VAL2=>4)
  ---------------------------
                            2
  ```

- During usage of overloading, ambiguity arises due to implicit conversion of data types

***Example*** for Standalone Deployment and YAC Deployment

  ```plsql
  CREATE OR REPLACE PACKAGE pack1 AS
      FUNCTION func(val1 NUMBER, val2 VARCHAR) RETURN NUMBER;
      FUNCTION func(val1 NUMBER, val2 NUMBER) RETURN NUMBER;
  END;
  /

  CREATE OR REPLACE PACKAGE BODY pack1 AS
      FUNCTION func(val1 NUMBER, val2 VARCHAR) RETURN NUMBER IS BEGIN RETURN 1; END;
      FUNCTION func(val1 NUMBER, val2 NUMBER) RETURN NUMBER IS BEGIN RETURN 2; END;
  END;
  /

  SELECT pack1.func(1, '2') FROM dual;
  -- Executes normally, value 1
  PACK1.FUNC(1,'2')
  -----------------
                  1
                  
  SELECT pack1.func(1, 2) FROM dual;
  -- Executes normally, value 2
  PACK1.FUNC(1,2)
  ---------------
                2

  SELECT pack1.func('1', '2') FROM dual;
  -- Execution fails, the system cannot identify which function to call 
  YAS-06924 too many declarations of 'FUNC' match this call

  SELECT pack1.func('1', 2) FROM dual;    
  -- Execution fails, the system cannot identify which function to call 
  YAS-06924 too many declarations of 'FUNC' match this call
  ```

## Deleting a Package

Use the [DROP PACKAGE](../../SQL Reference Manual/SQL Statements/DROP PACKAGE) statement to delete the package.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Creating UDF, calling the subprogram object from the above package
CREATE OR REPLACE FUNCTION calc(c CHAR) RETURN NUMBER IS
BEGIN
  calc_fee.calc_rev(c);
  RETURN calc_fee.c;
END;
/
SELECT calc('01') FROM dual;
 CALC('01') 
----------- 
      76666

-- Deleting the package
DROP PACKAGE calc_fee;
SELECT calc('01') FROM dual;
[1:8]YAS-04253 PL/SQL compiling errors:
[3:3] YAS-04243 invalid identifier "CALC_FEE"."CALC_REV"
[4:10] YAS-04243 invalid identifier "CALC_FEE"."C"
```

## Modifying a Package

Use the [ALTER PACKAGE](../../SQL Reference Manual/SQL Statements/ALTER PACKAGE) statement to modify the package.