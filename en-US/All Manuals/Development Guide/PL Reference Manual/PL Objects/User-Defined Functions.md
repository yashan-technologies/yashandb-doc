UDF is a type of PL object in the database, short for User-Defined Function.

YashanDB supports the direct creation of UDFs organized by PL language by function, and also supports calling external Java methods or external C functions in PL, implementing the external UDF functionality in the database.

The prerequisites for using UDF are as follows:

- UDF is not suitable for ISC Distributed Cluster Deployment.
- Ensure that the YashanDB server has the udf plugin installed (this can be confirmed by checking if the udf folder exists in the path $YASDB_HOME/third).

## User UDF

UDF is a database object organized by function in PL language, similar to functions in Pascal (structured programming language).

### Creating UDF

Use the [CREATE FUNCTION](../../SQL Reference Manual/SQL Statements/CREATE FUNCTION) statement to create a UDF, with the following syntax definition:

**create function::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] FUNCTION function_head_clause (IS|AS) function_body_clause ";"
```

**function_head_clause::=**

```ebnf+diagram
syntax::= [schema "."] function_name ["("  (argument_define) {"," (argument_define)}  ")"] 
 RETURN return_datatype [{PIPELINED|PARALLEL_ENABLE|DETERMINISTIC|invoker_rights_clause|aggregate_clause}]
```

**invoker_rights_clause::=**

```ebnf+diagram
syntax::= AUTHID (CURRENT_USER|DEFINER)
```

**aggregate_clause::=**

```ebnf+diagram
syntax::= AGGREGATE USING [schema "."] implementation_type
```

**function_body_clause::=**

```ebnf+diagram
syntax::= [variable_declare] BEGIN plsql_statements END [function_name]
```

#### 1. OR REPLACE

Rebuilds the UDF if it already exists.

#### 2. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, has no practical meaning.

#### 3. function\_name

The name of the UDF, which cannot be omitted and must comply with YashanDB's [object naming rules](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

#### 4. argument\_define

The parameters of the UDF (formal parameters), which can be omitted. In this case, function_name() = function_name. Up to 4095 parameters can be specified.

For more details, please refer to [PL Parameter Description](../Parameters/00Parameters).

#### 5. return\_datatype

The return type of the UDF; it is an implicit formal parameter and cannot have its length, precision, and other attributes specified.

<span id="pipelined" name="pipelined" class="yaslink"></span>

#### 6. PIPELINED

Used to identify pipeline table functions (shortened to pipeline functions).

Pipeline functions return collection types. Within the pipeline function body, a row is produced using the [PIPE ROW statement](../PL Statements/PIPE ROW Statement). Users can query the pipeline function using a SELECT statement to immediately obtain the row without waiting for the generation of the entire result set, which effectively shortens the query response time.

The following rules must be followed when using pipeline functions:

- Pipeline functions can have the same name as built-in functions.
- Pipeline functions are only supported as data sources for queries.
- The maximum row length of pipeline functions is always based on the smaller value specified in the [row length specifications](../../../Product Overview/Specifications/Logical Specifications.html#table) (i.e., the maximum row length is 64512 Bytes).
- Pipeline function parameters can only be input parameters; they cannot be output or input/output parameters.
- The RETURN statement of a pipeline function cannot specify a return value.
- If the pipeline function is not in an autonomous transaction, DML/DDL statements are not allowed.
- A pipeline function cannot be called by the procedure body, nor can it call itself or other pipeline functions.
- A pipeline function can only return collection types (except associative arrays); the rows written by PIPE ROW must be SQL types or global UDT types.
- The collection element type returned by a pipeline function cannot be of RECORD type.
- Currently, pipeline function parameters cannot use cursor types or cursor expressions.
- Currently, pipeline functions cannot produce DBMS_OUTPUT information.
- Modifications to package variables within a pipeline function are not visible to other threads.
- UDT methods defined as pipeline functions are currently not supported.
- Pipeline functions execute continuously in parallel threads. If there are DML statements in the function and the query conditions limit the number of rows to be fetched to less than the number of rows produced by the pipeline, the number of affected rows for the DML object is not fixed.

To specify a pipeline function created with PIPELINED as an independent function, use the CREATE FUNCTION statement in the function head. Pipeline functions can also be specified in the function declarations and definitions when creating a [Package](./自定义高级包).

#### 7. PARALLEL\_ENABLE

Used to identify functions for parallel execution, has no practical effect; only for syntax compatibility.

#### 8. DETERMINISTIC

Used to specify the creation of deterministic functions, which are functions that produce fixed outputs when the same inputs are provided. Such functions typically meet the following conditions:

- If the deterministic function is called multiple times with the same input parameters, the return values should be exactly the same.
- The deterministic function does not modify any external state.
- The deterministic function does not raise unhandled exceptions.

This has no practical effect, only for syntax compatibility.

#### 9. invoker\_rights\_clause

For details, please refer to [invoker_rights_clause](00PL Objects.html#invoker) related descriptions.

#### 10. aggregate\_clause

For details, please refer to [aggregate_clause](#aggregate).

#### 11. variable\_declare

Declaration of global variables within the PL procedure body, which can be omitted.

For more details, please refer to [PL Variable Declaration](../PL Language Fundamentals/Variables/00Variables).

#### 12. plsql\_statements

Defines the execution statements within the procedure body, which can be one or more of the [PL statements](../PL Statements/00PL Statements).

Each execution path within the procedure body (branches of control statements, branches of exception handling units) must contain at least one [RETURN Statement](../PL Statements/RETURN Statement).

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Ordinary function 
CREATE OR REPLACE FUNCTION ya_func(i INT) RETURN VARCHAR
IS
BEGIN
CASE i
WHEN 1 THEN
RETURN 'hello';
WHEN 2 THEN
RETURN 'world';
END CASE;
END ya_func;
/

-- Pipeline function 
CREATE OR REPLACE TYPE ya_pipe_func_typ1 IS TABLE OF INT;
/

CREATE OR REPLACE FUNCTION ya_pipe_func(p1 INT) RETURN ya_pipe_func_typ1 PIPELINED IS
BEGIN
    FOR i IN 1..p1 LOOP
      pipe row(i);
    END LOOP;
    RETURN;
END;
/

-- Aggregate function 
-- Creating a user-defined aggregate function mainly consists of two parts:
-- 1. Creating an OBJECT type that includes the ODCIAggregate function
CREATE OR REPLACE TYPE udt_impltype AS OBJECT(
    retstr      VARCHAR2(30000), 
    SEPARATOR   VARCHAR2(64),

    STATIC FUNCTION ODCIAGGREGATEINITIALIZE(sctx IN OUT udt_impltype) RETURN NUMBER,
    MEMBER FUNCTION ODCIAGGREGATEITERATE(self IN OUT udt_impltype, val IN VARCHAR2) RETURN NUMBER,
    MEMBER FUNCTION ODCIAGGREGATETERMINATE(self IN udt_impltype, returnvalue OUT VARCHAR2, flags IN NUMBER) RETURN NUMBER)
/

CREATE OR REPLACE TYPE BODY udt_impltype IS
    STATIC FUNCTION ODCIAGGREGATEINITIALIZE(sctx IN OUT udt_impltype) RETURN NUMBER IS
    BEGIN
        sctx := udt_impltype('', ',');
        RETURN ODCIConst.SUCCESS;
    END;

    MEMBER FUNCTION ODCIAGGREGATEITERATE(self IN OUT udt_impltype, val IN VARCHAR2) RETURN NUMBER IS
    BEGIN
        self.retstr := self.retstr || val||self.SEPARATOR;
        RETURN ODCIConst.SUCCESS;
    END;

    MEMBER FUNCTION ODCIAGGREGATETERMINATE(self IN udt_impltype, returnvalue OUT VARCHAR2, FLAGS IN NUMBER)
    RETURN NUMBER IS
    BEGIN
        returnvalue := SUBSTR(self.retstr,1,LENGTH(self.retstr)-1);
        RETURN ODCIConst.SUCCESS;
    END;
END;
/

-- 2. Creating a user-defined aggregate function 
CREATE OR REPLACE FUNCTION udf_aggr(i_str VARCHAR2) RETURN VARCHAR2
    AGGREGATE USING udt_impltype;
/
```

<span id="AggregateFunction" name="AggregateFunction" class="yaslink"></span>

### Creating User-Defined Aggregate Functions

The process of creating user-defined aggregate functions includes implementing the [ODCIAggregate function](#ODCIAggregate) and [defining the user-defined aggregate function](#aggregate).

User-defined aggregate functions can be used in the [select_list](../../SQL Reference Manual/SQL Statements/SELECT.html#select_list), HAVING clause, and ORDER BY clause as [aggregate functions](../../SQL Reference Manual/Built-in Functions/00Built-in Functions.html#aggregate-function), returning single-row data or grouped data according to GROUP, or can be used as [window functions](../../SQL Reference Manual/Built-in Functions/00Built-in Functions.html#window-function) using the OVER syntax.

The AGGREGATE field in the DBA/ALL/USER_PROCEDURES view can confirm whether a UDF is an aggregate function.

The usage constraints for user-defined aggregate functions are as follows:

- The aggregate_clause can only be used in standalone CREATE FUNCTION statements.
- User-defined aggregate functions cannot be used in stored procedures.
- User-defined aggregate functions do not support DISTINCT syntax for parameters.
- User-defined aggregate functions require at least one input parameter. If there are multiple parameters, the parameters from the second to the last are the input parameters for initiating the function.
- User-defined aggregate functions cannot use default values and cannot pass parameters by name.
- User-defined aggregate functions cannot return CURSOR types.
- User-defined aggregate functions are not allowed to nest other user-defined aggregate functions (i.e., their parameters cannot be user-defined aggregate functions), nor are they allowed to be nested with built-in aggregate functions in either direction.

<span id="ODCIAggregate" name="ODCIAggregate" class="yaslink"></span>

#### 1. ODCIAggregate Function

ODCIAggregate functions include the initialize, iterate, and terminate functions.

The function returns ODCIConst.SUCCESS when executed successfully, otherwise, an execution failure error is raised.

##### 1.1. Initialize Function

```plsql
STATIC FUNCTION ODCIAGGREGATEINITIALIZE (
        actx IN OUT <impltype>)
RETURN NUMBER;
```

ODCIAGGREGATEINITIALIZE is used to initialize the context of the user-defined aggregate function by constructing the function and returning through the OUT parameter.

Multiple parameters can be used for initialization, and the number of parameters must correspond to the number of parameters in the user-defined aggregate function.

|Parameter |Description |
| :---- |:-----------|
| actx | The context of the user-defined aggregate function, which is an OBJECT type variable entity that is passed between the ODCIAggregate functions during execution. |

##### 1.2. Iterate Function

```plsql
MEMBER FUNCTION ODCIAGGREGATEITERATE (
        self IN OUT <impltype>,
        val <inputdatatype>)
RETURN NUMBER;
```

ODCIAGGREGATEITERATE is used to update the context of the user-defined aggregate function based on input values and return, executing only once for each row.

|Parameter |Description |
| :---- |:-----------|
| self | The context of the user-defined aggregate function, updated at the end of the execution. |
| val | The first input parameter value of the user-defined aggregate function for each row. <br/>If val is NULL, that row is not executed. |

##### 1.3. Terminate Function

```plsql
MEMBER FUNCTION ODCIAGGREGATETERMINATE (
        self IN OUT <impltype>,
        ReturnValue OUT <return_type>,
        flags IN number)
RETURN NUMBER;
```

ODCIAGGREGATETERMINATE is used to compute the return result of the user-defined aggregate function.

|Parameter |Description |
| :---- |:-----------|
| self | The context of the user-defined aggregate function. |
| ReturnValue | The return result of the user-defined aggregate function. |
| flags | Used for syntax compatibility, has no practical meaning. |

<span id="aggregate" name="aggregate" class="yaslink"></span>

#### 2. aggregate\_clause

This clause is used to define user-defined aggregate functions.

##### 2.1. AGGREGATE USING

Used to identify aggregate functions.

##### 2.2. [schema.]implementation\_type

Used to specify the type of implementation that the aggregate function needs to execute, which must be of the OBJECT type implemented by [ODCIAggregate function](#ODCIAggregate).

The schema name can be omitted; if omitted, it defaults to the schema of the user-defined aggregate function.

When executed, user-defined aggregate functions automatically invoke the corresponding initialize, iterate, and terminate functions, returning the final aggregation or grouping results.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Aggregate function 
-- 1. Create an OBJECT type that includes the ODCIAggregate function
CREATE OR REPLACE TYPE udt_impltype AS OBJECT(
    retstr      VARCHAR2(30000), 
    SEPARATOR   VARCHAR2(64),

    STATIC FUNCTION ODCIAGGREGATEINITIALIZE(sctx IN OUT udt_impltype) RETURN NUMBER,
    MEMBER FUNCTION ODCIAGGREGATEITERATE(self IN OUT udt_impltype, val IN VARCHAR2) RETURN NUMBER,
    MEMBER FUNCTION ODCIAGGREGATETERMINATE(self IN udt_impltype, returnvalue OUT VARCHAR2, flags IN NUMBER) RETURN NUMBER)
/

CREATE OR REPLACE TYPE BODY udt_impltype IS
    STATIC FUNCTION ODCIAGGREGATEINITIALIZE(sctx IN OUT udt_impltype) RETURN NUMBER IS
    BEGIN
        sctx := udt_impltype('', ',');
        RETURN ODCIConst.SUCCESS;
    END;

    MEMBER FUNCTION ODCIAGGREGATEITERATE(self IN OUT udt_impltype, val IN VARCHAR2) RETURN NUMBER IS
    BEGIN
        self.retstr := self.retstr || val||self.SEPARATOR;
        RETURN ODCIConst.SUCCESS;
    END;

    MEMBER FUNCTION ODCIAGGREGATETERMINATE(self IN udt_impltype, returnvalue OUT VARCHAR2, FLAGS IN NUMBER)
    RETURN NUMBER IS
    BEGIN
        returnvalue := SUBSTR(self.retstr,1,LENGTH(self.retstr)-1);
        RETURN ODCIConst.SUCCESS;
    END;
END;
/

-- 2. Create a user-defined aggregate function 
CREATE OR REPLACE FUNCTION udf_aggr(i_str VARCHAR2) RETURN VARCHAR2
    AGGREGATE USING udt_impltype;
/
```

### Using UDF

#### Calling in SQL Statement

The method of calling is the same as that for [built-in functions](../../SQL Reference Manual/Built-in Functions/00Built-in Functions), used as SQL expressions, for example:

_SELECT function_name(arguments) FROM dual;_

The arguments correspond one-to-one with [actual parameters](../Parameters/Formal and Actual Parameters), and the argument_define cannot be IN OUT or OUT [formal parameters](../Parameters/Formal and Actual Parameters).

In SELECT statements, `*` cannot be used as a parameter for UDF.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SELECT ya_func(1)||' '||ya_func(2) res FROM dual;
RES                                             
---------------------------------------------------------------- 
hello world 
```

#### Calling in Procedure Body

The method of calling is the same as that for [built-in functions](../../SQL Reference Manual/Built-in Functions/00Built-in Functions), used as expressions in PL statements or for assigning values to variables, for example:

_variable_name := procedure_name(arguments);_

The arguments correspond one-to-one with [actual parameters](../Parameters/Formal and Actual Parameters).

***Example*** for Standalone Deployment and YAC Deployment

```plsql
DECLARE
  a VARCHAR(10);
  b VARCHAR(10);
BEGIN
  a := ya_func(1);
  b := ya_func(2);
  DBMS_OUTPUT.PUT_LINE(a||' '||b);
END;
/

--result
hello world
```

#### Querying Pipeline Functions

Query the pipeline functions using SELECT statements; the TABLE keyword is optional.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SELECT * FROM TABLE(ya_pipe_func(5));
COLUMN_VALUE 
------------ 
           1
           2
           3
           4
           5

SELECT * FROM TABLE(ya_pkg1.ya_pkg_pipe_func(6));
COLUMN_VALUE 
------------ 
           1
           2
           3
           4
           5
           6

-- TABLE keyword can be omitted
SELECT * FROM ya_pipe_func(5);
COLUMN_VALUE 
------------ 
           1
           2
           3
           4
           5

SELECT * FROM ya_pkg1.ya_pkg_pipe_func(6);
COLUMN_VALUE 
------------ 
           1
           2
           3
           4
           5
           6

SELECT * FROM ya_pipe_func(6) a WHERE a.column_value NOT IN (SELECT * FROM ya_pipe_func(5));
COLUMN_VALUE
------------
           6
```

The result set of the pipeline function projections has only one column, named COLUMN_VALUE. If the projected column type is UDT_OBJECT, the result set projection column automatically expands to the property columns of the object type.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
CREATE OR REPLACE TYPE ya_pipe_type_obj1 IS OBJECT(C1 INT, C2 VARCHAR(100));
/

CREATE OR REPLACE TYPE YA_PIPE_TYPE2 IS TABLE OF ya_pipe_type_obj1;
/

CREATE OR REPLACE FUNCTION ya_pipe_fun2(p1 INT) RETURN YA_PIPE_TYPE2 PIPELINED IS
    REC1 ya_pipe_type_obj1 := ya_pipe_type_obj1(NULL, NULL);
BEGIN
    FOR i IN 1..p1 LOOP
        rec1.c1 := i;
        rec1.c2 := CONCAT('pipe row ' , i);
        pipe row(rec1);
    END LOOP;
    RETURN;
END;
/

SELECT * FROM TABLE(ya_pipe_fun2(3));
          C1 C2                                                               
------------ ---------------------------------------------------------------- 
           1 pipe row 1                                                      
           2 pipe row 2                                                      
           3 pipe row 3                                                      
```

The query statement triggers the execution of the pipeline function, and when the query conditions are met and there is no need for subsequent row data, the PIPE ROW statement will throw a NO_DATA_NEEDED exception.

#### Using User-Defined Aggregate Functions

- Using as an aggregate function

    Example (Standalone, YAC Deployment)

    ```sql
    SELECT udf_aggr(employee_name) res FROM sales.employees;

    RES
    ----------------------------------------------------------------
    Mask,John,Anna,Jack,Jim
    ```
- Using as a window function with OVER syntax

    Example (Standalone, YAC Deployment)

    ```sql
    SELECT department, udf_aggr(employee_name) OVER (PARTITION BY department ORDER BY entry_date) res FROM sales.employees;

    DEPARTMENT RES
    ---------- ----------------------------------------------------------------
    000        John
    000        John,Mask
    008        Jack
    008        Jack,Jim
    010        Anna
    ```

### Managing UDF

#### Deleting UDF

Use the [DROP FUNCTION](../../SQL Reference Manual/SQL Statements/DROP FUNCTION) statement to delete a UDF.

#### Modifying UDF

Use the [ALTER FUNCTION](../../SQL Reference Manual/SQL Statements/ALTER FUNCTION) statement to modify a UDF.

<span id="ext_udf" name="ext_udf" class="yaslink"></span>

## External UDF

External UDF is a special form of UDF, which includes external Java language and external C language, abbreviated as Ext-UDF or external UDF.

### Environment Requirements (Java)

Using external UDF in Java requires that the database server has JDK (version 1.8 or higher) installed, and the following environment variables are configured:

```shell
# Replace the following path with the actual JDK installation path
$ export LD_LIBRARY_PATH=/etc/jdk-18.0.2/lib/server:$LD_LIBRARY_PATH
```

### Creating User-Defined Libraries

To use external UDFs, the required [User-Defined Libraries](User-Defined Librarys) must first be created in the database using the CREATE LIBRARY statement.

- For external UDFs in Java: YashanDB supports creating user-defined libraries containing .class files or jar packages that include user-defined Java functions.
- For external UDFs in C: YashanDB supports creating dynamic library files in .dll (Windows) and .so (Linux) formats that include C functions.

During the creation of user-defined libraries, the existence of library files is not checked; instead, the library files are checked and loaded when executing the external UDF. If the content of class files, jar packages, so files, or dll files changes, the user-defined library must be regenerated.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Create user-defined library for Java
CREATE OR REPLACE LIBRARY ya_java_lib IS
'/home/yasdb/example/UDFexample.class';
/
```

The UDFexample.class corresponds to the following content of the UDFexample.java file:

```java
package example;

public class UDFexample {
    public static String execJdbcexample(int ctrls) {
        switch (ctrls) {
        case 1:return "Hello";
        case 2:return "World";
        default:return "!";
        }
    }

    public static void main(String[] args) {
       String a = execJdbcexample(1);
    }
}
```

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Create user-defined library for C
CREATE OR REPLACE LIBRARY ya_c_lib IS
'/home/yasdb/example/libUDFexample.so';
/
```

The libUDFexample.so corresponds to the content of the UDFexample.c file as follows:

```C
#include "string.h"
#include "yacli.h"
    
YacResult udfExample(YacHandle hProc)
{
    YacInt32  id;
    YacChar   buf[1024];

    YAC_CALL(yepGetInt32(hProc, 0, &id, NULL));
    (YacVoid)snprintf(buf, 1024, "Hello World, id: %d", id);
    YAC_CALL(yepReturnString(hProc, buf));

    return YAC_SUCCESS;
}
```

### Defining External UDF in Java

Define an external UDF in Java by creating a function, with the following syntax:

```ebnf+diagram
syntax::= CREATE [OR REPLACE] FUNCTION [schema "."] function_name ["("  (argument_define) {"," (argument_define)}  ")"] RETURN return_datatype (IS|AS) call_spec ";"
```

For the purpose of passing parameters to external UDF and obtaining return values, the parameters and return values in the function definition should be consistent with those in the java_method, meaning:

- The number and order of parameters should be the same.
- Data types must be compatible, i.e., explicitly satisfying the corresponding relationship between YashanDB and Java data types or meeting the corresponding relationship after implicit conversion, as described below.

**call\_spec::=**

```ebnf+diagram
syntax::= LANGUAGE JAVA NAME "java_string_literal_name" LIBRARY ([schema"."]library_name | "'" library_path "'")
```

#### LANGUAGE

Specifies the language used by the external UDF, here it is JAVA.

#### NAME

Specifies the function signature of the Java function to be referenced. The java_package.java_class part must be less than 128 bytes and be formatted as follows:

*java_package.java_class.java_method(argu1_datatype,argu2_datatype...) [return return_datatype]*

The referenced Java function must be a static Java method, and its parameter and return types must fall within the following list of Java data types:

- bool: corresponds to YashanDB's BOOLEAN
- byte: corresponds to YashanDB's TINYINT
- char: corresponds to YashanDB's INT (value range [0, 65535], i.e., 0-16 bit unsigned integer)
- short: corresponds to YashanDB's SMALLINT
- int: corresponds to YashanDB's INT
- long: corresponds to YashanDB's BIGINT
- float: corresponds to YashanDB's FLOAT
- double: corresponds to YashanDB's DOUBLE
- string: corresponds to YashanDB's VARCHAR

#### library\_name

Specifies the user-defined library name that has already been created.

#### library\_path

Method One: Specify the library during function creation as the name of the library that has been successfully created.

Method Two: Specify the library during function creation as the path of the user-defined library loaded via the [DBMS_STANDARD](../Built-in Advanced PL Packages/DBMS_STANDARD).LOADJAVA method (this method is not recommended).

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Method One
CREATE OR REPLACE FUNCTION udf_func_java(argu INT) RETURN VARCHAR IS
LANGUAGE java
NAME 'example.UDFexample.execJdbcexample(int) return string'
LIBRARY ya_java_lib;
/

-- Method Two
call DBMS_STANDARD.LOADJAVA('/home/yasdb/example/UDFexample.class');
CREATE OR REPLACE FUNCTION udf_func_java(argu INT) RETURN VARCHAR IS
LANGUAGE java
NAME 'example.UDFexample.execJdbcexample(int) return string'
LIBRARY '/home/yasdb/example/UDFexample.class';
/
```

### Defining External UDF in C

Define an external UDF in C by creating a function, with the following syntax:

```ebnf+diagram
syntax::= CREATE [OR REPLACE] FUNCTION [schema "."] function_name ["("  (argument_define) {"," (argument_define)}  ")"] RETURN return_datatype (IS|AS) call_spec ";"
```

**call\_spec::=**

```ebnf+diagram
syntax::= (LANGUAGE C | EXTERNAL) (([NAME c_string_literal_name] LIBRARY [schema"."] library_name)|(LIBRARY [schema"."] library_name [NAME c_string_literal_name]))
```

#### LANGUAGE

Specifies the language used by the external UDF, here it is C.

#### EXTERNAL

Specifies this as an external UDF in C (it is recommended to use LANGUAGE to specify the language of the external UDF).

#### NAME

Specifies the function name of the C function to be referenced through c_string_literal_name. It defaults to uppercase; if the case needs to be preserved, use double quotes. If NAME is omitted, the external UDF's name is used by default.

c_string_literal_name must be a valid [identifier](../../SQL Reference Manual/Basic SQL Elements/Identifiers), with a maximum length of 64 bytes.

#### library\_name

Specifies the user-defined library name that has already been created.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
CREATE OR REPLACE FUNCTION udf_func_c(argu INT) RETURN VARCHAR IS
LANGUAGE C
NAME "udfExample"
LIBRARY ya_c_lib;
/
```

### Guidelines for Writing C Language External UDF

C language external UDFs are functions written in C, compiled into dynamic library files, and subsequently called by the database when created as user-defined libraries.

C functions have a single parameter hProc, which must not be modified. The functionality is implemented using a series of YashanDB-provided yepGet, yepOutput, and yepReturn interfaces to obtain input parameters, set output parameters, and set return values.

When calling the yepGet interface, ensure to call the same type or a type that can be implicitly converted with the yepGet interface based on the input parameter type.

When calling the yepOutput interface, ensure to call the same type of yepOutput interface based on the input parameter type.

When calling the yepReturn interface, ensure to call the same type or a type that can be implicitly converted with the yepReturn interface, and the UDF must call the yepReturn interface to set the return value.

The interface parameter id corresponds to the parameter number defined in the function, starting from 0.

The interface parameter lenOrInd is used to obtain the length of the input parameter or to check if it is NULL; if its value returns YAC_NULL_DATA, it indicates that the input parameter is NULL; otherwise, it returns the byte length of the input parameter. lenOrInd can be NULL.

YashanDB provides the following interfaces for writing C language dynamic libraries:

|Interface Name |Interface Function Declaration |Interface Usage Description |
| ---------| ------------ | ----------- |
| yepGetCharsetId | YacResult yepGetCharsetId(YacHandle hProc, YacUint16* charsetId) | Get the database character set ID, assign it to charsetId. |
| yepGetBool | YacResult yepGetBool(YacHandle hProc, YacInt32 id, YacBool* v, YacInt32* lenOrInd) | Get the value of the BOOLEAN type input parameter at id, assign it to v. |
| yepGetInt8 | YacResult yepGetInt8(YacHandle hProc, YacInt32 id, YacInt8* v, YacInt32* lenOrInd) | Get the value of the TINYINT type input parameter at id, assign it to v. |
| yepGetInt16 | YacResult yepGetInt16(YacHandle hProc, YacInt32 id, YacInt16* v, YacInt32* lenOrInd) | Get the value of the SMALLINT type input parameter at id, assign it to v. |
| yepGetInt32 | YacResult yepGetInt32(YacHandle hProc, YacInt32 id, YacInt32* v, YacInt32* lenOrInd) | Get the value of the INT type input parameter at id, assign it to v. |
| yepGetInt64 | YacResult yepGetInt64(YacHandle hProc, YacInt32 id, YacInt64* v, YacInt32* lenOrInd) | Get the value of the BIGINT type input parameter at id, assign it to v. |
| yepGetFloat | YacResult yepGetFloat(YacHandle hProc, YacInt32 id, YacFloat* v, YacInt32* lenOrInd) | Get the value of the FLOAT type input parameter at id, assign it to v. |
| yepGetDouble | YacResult yepGetDouble(YacHandle hProc, YacInt32 id, YacDouble* v, YacInt32* lenOrInd) | Get the value of the DOUBLE type input parameter at id, assign it to v. |
| yepGetNumber | YacResult yepGetNumber(YacHandle hProc, YacInt32 id, YacNumber* v, YacInt32* lenOrInd) | Get the value of the NUMBER type input parameter at id, assign it to v. |
| yepGetDate | YacResult yepGetDate(YacHandle hProc, YacInt32 id, YacDate* v, YacInt32* lenOrInd) | Get the value of the DATE type input parameter at id, assign it to v. |
| yepGetTimestamp | YacResult yepGetTimestamp(YacHandle hProc, YacInt32 id, YacTimestamp* v, YacInt32* lenOrInd) | Get the value of the TIMESTAMP type input parameter at id, assign it to v. |
| yepGetYMInterval | YacResult yepGetYMInterval(YacHandle hProc, YacInt32 id, YacYMInterval* v, YacInt32* lenOrInd) | Get the value of the INTERVAL YEAR TO MONTH type input parameter at id, assign it to v. |
| yepGetDSInterval | YacResult yepGetDSInterval(YacHandle hProc, YacInt32 id, YacDSInterval* v, YacInt32* lenOrInd) | Get the value of the INTERVAL DAY TO SECOND type input parameter at id, assign it to v. |
| yepGetString | YacResult yepGetString(YacHandle hProc, YacInt32 id, YacChar* str, YacUint32 bufSize, YacInt32* lenOrInd) | Get the value of the string type (CHAR, VARCHAR) input parameter at id, needs pre-allocated memory, passing the pointer str and bufSize. |
| yepGetBytes | YacResult yepGetBytes(YacHandle hProc, YacInt32 id, YacUint8* bytes, YacUint32 bufSize, YacInt32* lenOrInd) | Get the value of the RAW type input parameter at id, needs pre-allocated memory, passing the pointer bytes and bufSize. |
| yepOutputNull | YacResult yepOutputNull(YacHandle hProc, YacInt32 id) | Set the id-th output parameter to NULL. |
| yepOutputBool | YacResult yepOutputBool(YacHandle hProc, YacInt32 id, YacBool v) | Set the value of the id-th BOOLEAN type output parameter to v. |
| yepOutputInt8 | YacResult yepOutputInt8(YacHandle hProc, YacInt32 id, YacInt8 v) | Set the value of the id-th TINYINT type output parameter to v. |
| yepOutputInt16 | YacResult yepOutputInt16(YacHandle hProc, YacInt32 id, YacInt16 v) | Set the value of the id-th SMALLINT type output parameter to v. |
| yepOutputInt32 | YacResult yepOutputInt32(YacHandle hProc, YacInt32 id, YacInt32 v) | Set the value of the id-th INT type output parameter to v. |
| yepOutputInt64 | YacResult yepOutputInt64(YacHandle hProc, YacInt32 id, YacInt64 v) | Set the value of the id-th BIGINT type output parameter to v. |
| yepOutputFloat | YacResult yepOutputFloat(YacHandle hProc, YacInt32 id, YacFloat v) | Set the value of the id-th FLOAT type output parameter to v. |
| yepOutputDouble | YacResult yepOutputDouble(YacHandle hProc, YacInt32 id, YacDouble v) | Set the value of the id-th DOUBLE type output parameter to v. |
| yepOutputNumber | YacResult yepOutputNumber(YacHandle hProc, YacInt32 id, YacNumber* v) | Set the value of the id-th NUMBER type output parameter to v. |
| yepOutputDate | YacResult yepOutputDate(YacHandle hProc, YacInt32 id, YacDate v) | Set the value of the id-th DATE type output parameter to v. |
| yepOutputTimestamp | YacResult yepOutputTimestamp(YacHandle hProc, YacInt32 id, YacTimestamp* v) | Set the value of the id-th TIMESTAMP type output parameter to v. |
| yepOutputYMInterval | YacResult yepOutputYMInterval(YacHandle hProc, YacInt32 id, YacYMInterval v) | Set the value of the id-th INTERVAL YEAR TO MONTH type output parameter to v. |
| yepOutputDSInterval | YacResult yepOutputDSInterval(YacHandle hProc, YacInt32 id, YacDSInterval v) | Set the value of the id-th INTERVAL DAY TO SECOND type output parameter to v. |
| yepOutputString | YacResult yepOutputString(YacHandle hProc, YacInt32 id, YacChar* str) | Set the value of the id-th output parameter of string type (CHAR, VARCHAR) to the string pointed to by str. |
| yepOutputBytes | YacResult yepOutputBytes(YacHandle hProc, YacInt32 id, YacUint8* bytes, YacUint32 size) | Set the value of the id-th RAW type output parameter to the bytes pointed to by bytes, size is the size. |
| yepReturnNull | #define yepReturnNull(hProc) yepOutputNull(hProc, YEP_RETURN) | Set the return value to NULL. |
| yepReturnBool | #define yepReturnBool(hProc, value) yepOutputBool(hProc, YEP_RETURN, value) | Set the return value to BOOLEAN type, the value is value. |
| yepReturnInt8 | #define yepReturnInt8(hProc, value) yepOutputInt8(hProc, YEP_RETURN, value) | Set the return value to TINYINT type, the value is value. |
| yepReturnInt16 | #define yepReturnInt16(hProc, value) yepOutputInt16(hProc, YEP_RETURN, value) | Set the return value to SMALLINT type, the value is value. |
| yepReturnInt32 | #define yepReturnInt32(hProc, value) yepOutputInt32(hProc, YEP_RETURN, value) | Set the return value to INT type, the value is value. |
| yepReturnInt64 | #define yepReturnInt64(hProc, value) yepOutputInt64(hProc, YEP_RETURN, value) | Set the return value to BIGINT type, the value is value. |
| yepReturnFloat | #define yepReturnFloat(hProc, value) yepOutputFloat(hProc, YEP_RETURN, value) | Set the return value to FLOAT type, the value is value. |
| yepReturnDouble | #define yepReturnDouble(hProc, value) yepOutputDouble(hProc, YEP_RETURN, value) | Set the return value to DOUBLE type, the value is value. |
| yepReturnNumber | #define yepReturnNumber(hProc, value) yepOutputNumber(hProc, YEP_RETURN, value) | Set the return value to NUMBER type, the value is value. |
| yepReturnDate | #define yepReturnDate(hProc, value) yepOutputDate(hProc, YEP_RETURN, value) | Set the return value to DATE type, the value is value. |
| yepReturnTimestamp | #define yepReturnTimestamp(hProc, value) yepOutputTimestamp(hProc, YEP_RETURN, value) | Set the return value to TIMESTAMP type, the value is value. |
| yepReturnYMInterval | #define yepReturnYMInterval(hProc, value) yepOutputYMInterval(hProc, YEP_RETURN, value) | Set the return value to INTERVAL YEAR TO MONTH type, the value is value. |
| yepReturnDSInterval | #define yepReturnDSInterval(hProc, value) yepOutputDSInterval(hProc, YEP_RETURN, value) | Set the return value to INTERVAL DAY TO SECOND type, the value is value. |
| yepReturnString | #define yepReturnString(hProc, value) yepOutputString(hProc, YEP_RETURN, value) | Set the return value to VARCHAR type, the value is the string pointed to by value. |
| yepReturnBytes | #define yepReturnBytes(hProc, value, size) yepOutputBytes(hProc, YEP_RETURN, value, size) | Set the return value to RAW type, the bytes point to the bytes, size is the size. |

### Using External UDF

For successfully defined external UDFs, calling methods are consistent with functions.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SELECT udf_func_java(1) FROM dual;
UDF_FUNC_JAVA(1)                                                      
---------------------------------------------------------------- 
Hello  

SELECT udf_func_java(2) FROM dual;
UDF_FUNC_JAVA(2)                                                      
---------------------------------------------------------------- 
World 

SELECT udf_func_c(1) FROM dual;
UDF_FUNC_C(1)
----------------------------------------------------------------
Hello World, id: 1
```

### Deleting External UDF

Deleting the function that defines a certain external UDF effectively deletes that external UDF.

### Deleting User-defined Libraries

Once it is confirmed that the user-defined library used by the external UDF is no longer needed, it can be deleted using the [DROP_LIBRARY](../../SQL Reference Manual/SQL Statements/DROP LIBRARY) statement.
