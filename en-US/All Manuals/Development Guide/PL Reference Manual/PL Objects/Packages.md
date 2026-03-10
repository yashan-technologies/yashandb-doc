Package is a type of PL object in the database.

Package is not suitable for ISC Distributed Cluster Deployment.

## Creating a Package

The creation of a package consists of two parts:


- Define Package head
    
    Package head is used to declare public (PUBLIC attribute) members, which can include variables, types, cursors, and subprograms (stored procedures, UDFs). After package creation is successful, public members can be referenced by other external programs.

- Define Package body
    
    Package body is used to define subprograms in public members and to declare and define private (PRIVATE attribute) members. Private members can include variables, types, cursors, and subprograms, and private members can only be used within that package body.

Package head and body are defined through different syntax, and YashanDB does not strictly require the creation order of the same package package head and body. Even if the body is defined before the head, creation can still succeed, but the package will throw compilation errors due to the absence of the package head, and will remain unable to execute and call until the package head creation is completed.



The declaration and definition constraints for package members are as follows:

- Multiple members of different types can be cross-declared with no order restrictions. However, the order of members determines their visibility, with subsequent members being invisible to preceding members.

- The same member (name case-sensitive) is not allowed to be declared repeatedly, and public members and private members of the same package cannot have the same name.

- All declared subprograms in the same package must be defined in the package body.

- Private members must be declared before they are defined (i.e., in the package body, member declarations must be located before their definitions).

- All subprogram definitions must be located after variable definitions.

- User defined aggregate functions cannot be members.

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
syntax::= (variable_declare|procedure_declare|func_declare)
```

[variable_declare](../PL语言基础/变量/00变量) (The actual syntax depends on the specific variable type)

**procedure_declare::=**

```ebnf+diagram
syntax::= PROCEDURE procedure_name ["(" (argument_define) {"," (argument_define)} ")"] [RETURN return_datatype]
```

**func_declare::=**

```ebnf+diagram
syntax::= FUNCTION function_name ["(" (argument_define) {"," (argument_define)} ")"] RETURN return_datatype [{PIPELINED|PARALLEL_ENABLE|DETERMINISTIC}]
```

**package_body_clause::=**

```ebnf+diagram
syntax::= BODY [schema "."] package_name (IS|AS) [package_item_clause] package_subprogram_clause
```

**package_subprogram_clause::=**

```ebnf+diagram
syntax::= (IS|AS) (procedure_body_clause|function_body_clause)
```

[procedure_body_clause](./存储过程.html#procedurebodyclause)

[function_body_clause](./自定义函数.html#functionbodyclause)

### 1. OR REPLACE

When the package to be created already exists, it will be rebuilt.

### 2. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no actual meaning.

### 3. package\_name

The name of the package, which cannot be omitted and must conform to YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 4. package\_head\_clause

Creates a PACKAGE HEAD for a package.

#### 4.1. invoker\_rights\_clause

For details, please refer to the description of [invoker_rights_clause](00PL Objects.html#invoker).

#### 4.2. pragma\_clause

Optional, indicating whether the package variables are reset to the default values defined at the time of declaration each time they are referenced. By default, they are not reset.

In YashanDB, variables declared in the package are session-level global variables, meaning that in the same session, variable assignments affect each other, while variables in different sessions are independent. By setting SERIALLY_REUSABLE, the variable can regain its initial value upon each reference (from client to server).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

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

Used to declare package public members, which can be variables, types, cursors, or subprograms (stored procedures, UDFs), but cannot be user defined aggregate functions.

- Multiple members including variables, stored procedures, or functions can be cross-declared with no order restrictions. However, the order of members determines their visibility, with subsequent members being invisible to preceding members.

- The same member (name case-sensitive) is not allowed to be declared repeatedly.

- Each package_item_clause declares 1 member, and each statement for creating a package head can have up to 1536 package_item_clause clauses.



##### 4.3.1. variable\_declare

Used to explicitly declare variables. For variable types that can be declared and corresponding syntax details, please refer to [Variables](../PL语言基础/变量/00变量).

- For cursors, only explicit definition is allowed, explicit declaration is not permitted.

- Multiple different types of variables can be declared simultaneously, and the order is unrestricted.

##### 4.3.2. procedure\_declare

Used to declare sub-stored procedures.

##### 4.3.3. func\_declare

Used to declare sub-functions. For syntax details, please refer to [User Defined Functions](./自定义函数).



### 5. package\_body\_clause

Creates a PACKAGE BODY for a package.

Each package_item_clause declares 1 member, each package_subprogram_clause defines 1 subprogram (stored procedure or UDF) member, and the total number of package_item_clause and package_subprogram_clause in each statement for creating a package body can be up to 1536.

#### 5.1. package\_item\_clause

Used to declare private members of the package body, which can be variables, types, cursors, or subprograms (stored procedures, UDFs), but cannot be user defined aggregate functions.

- Multiple members of different types can be cross-declared with no order restrictions. However, the order of members determines their visibility, with subsequent members being invisible to preceding members.

- Private member declarations must be located after their definitions.



##### 5.1.1. variable\_declare

Used to explicitly declare variables. For variable types that can be declared and corresponding syntax details, please refer to [Variables](../PL语言基础/变量/00变量).

- For cursors, only explicit definition is allowed, explicit declaration is not permitted.

- Multiple different types of variables can be declared simultaneously, and the order is unrestricted.

##### 5.1.2. procedure\_declare

Used to declare sub-stored procedures.

##### 5.1.3. func\_declare

Used to declare sub-functions. For syntax details, please refer to [User Defined Functions](./自定义函数).



User defined aggregate functions cannot be private members.

#### 5.2. package\_subprogram\_clause

Used to define all subprograms of package, including stored procedures or custom functions in both public and private members.

- All declared subprograms in the same package must be defined in the package body.

- Private members must be declared before they are defined (i.e., in the package body, member definitions must be located after their declarations).

- All subprogram definitions must be located after variable definitions.

##### 5.2.1. procedure\_body\_clause

Used to define declared stored procedures, with syntax same as [Stored Procedures](./存储过程.html#procedurebodyclause).

##### 5.2.2. function\_body\_clause

Used to define declared UDFs, with syntax same as [User Defined Functions](./存储过程.html#procedurebodyclause).

If a UDF specifies different PIPELINED, PARALLEL_ENABLE, and DETERMINISTIC identifiers during declaration and definition, the effectiveness will vary depending on whether the function is a public or private member:

- If the function is a public member:

  - [PIPELINED](./自定义函数.html#pipelined): Must be consistent between declaration and definition, otherwise an error is reported.

  - PARALLEL_ENABLE: Always for syntax compatibility only. If not specified during declaration but specified during definition, an error is reported.

  - [DETERMINISTIC](./自定义函数.html#deterministic): Takes effect based on what is specified during declaration. If not specified during declaration but specified during definition, an error is reported.

- If the function is a private member, the above identifiers take effect based on what is specified during definition.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Creating a package without PACKAGE HEAD
DROP PACKAGE IF EXISTS calc_fee;

-- Creating BODY of the package, it will return an error as related variables are not declared, but the BODY is already successfully created
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

-- Creating PACKAGE HEAD for calc_fee (this step will not require to create BODY again)
CREATE OR REPLACE PACKAGE calc_fee
AS
c NUMBER := 100;
PROCEDURE calc_rev(prefix CHAR);
END calc_fee;
/

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

-- Create an advanced package with private members
CREATE OR REPLACE PACKAGE pkg_pri_member IS
FUNCTION func_pub RETURN INT;
END;
/

CREATE OR REPLACE PACKAGE body pkg_pri_member IS
FUNCTION func_PRI(emp_no IN VARCHAR2) RETURN INT;
function func_pub return int is
BEGIN
  RETURN func_PRI('hello');
END;
FUNCTION func_PRI(emp_no IN VARCHAR2) RETURN INT IS
BEGIN
 RETURN 100;
END;
END;
/
```

## Loading Variables from Packages

Package variables cannot be directly accessed in external SQL statements but must be loaded and used within subprograms (internal or external) in the following format:

*\[schema.\]\[package_name.\]variable_name;*

When called by external subprograms, the package_name cannot be omitted.

In YashanDB, the variables of the package are managed in binding with the package, meaning that if the PACKAGE HEAD or PACKAGE BODY of the package changes, all variables of that package will be reset.

## Running Subprogram Objects of a Package

**Calling Sub Function in SQL Statement**

Sub functions of the package can be invoked within SQL statements in various positions such as column items, condition items, or query options (like ORDER BY/LIMIT/OFFSET). For example, the calling format that appears in the query column is:

*SELECT package_name.function_name[(arguments)] FROM table_name;*

**Calling Sub Stored Procedures using CALL/EXEC Statements**

Sub stored procedures of the package can be called using CALL/EXEC statements, with the calling format:

 *(CALL|EXEC) package_name.procedure_name[(arguments)];*

**Calling Sub Stored Procedures within the Body of Procedures**

Sub stored procedures can be called within the body (internal or external) using the format:

*[package_name.]procedure_name[(arguments)];*

When called by external subprograms, the package_name cannot be omitted.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
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


-- Public members of the advanced package can be normally called in SQL statements
SELECT pkg_pri_member.func_pub FROM dual;

PKG_PRI_MEMBER.FUNC_PUB
----------------------------
                         100

-- Private members of the advanced package cannot be called in SQL statements
SELECT pkg_pri_member.func_pri('world') FROM dual;

[1:8]YAS-04243 invalid identifier "PKG_PRI_MEMBER"."FUNC_PRI"
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

***Example*** for Standalone/YAC/Distributed Cluster Deployment

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

  Example (Standalone, YAC/Distributed Cluster Deployment)

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

***Example*** for Standalone/YAC/Distributed Cluster Deployment

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

***Example*** for Standalone/YAC/Distributed Cluster Deployment

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

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Creating a UDF, calling the subprogram object from the above package
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

Use the [ALTER PACKAGE](../../SQL参考手册/SQL语句/ALTER PACKAGE) statement to modify the package.
