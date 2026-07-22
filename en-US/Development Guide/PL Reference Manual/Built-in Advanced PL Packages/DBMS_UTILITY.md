The DBMS_UTILITY package provides a set of built-in utility stored procedures/functions.

## ACTIVE_INSTANCES

```plsql
TYPE INSTANCE_RECORD IS RECORD (inst_number NUMBER, inst_name VARCHAR(64));
TYPE INSTANCE_TABLE IS TABLE OF INSTANCE_RECORD;

DBMS_UTILITY.ACTIVE_INSTANCES (
   instance_table   OUT INSTANCE_TABLE,
   instance_count   OUT NUMBER);
```

The ACTIVE_INSTANCES procedure is used to view information about active database instances, returning the number of active instances as well as the ID and name of each active instance. This procedure is not applicable to ISC Distributed Cluster Deployment.

|Parameter |Description |
| :--- | :---- |
| instance_table   | A list containing the numbers and names of active instances. The list is empty when no instances are started. |
| instance_count   | The number of active instances.                |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
set serveroutput on
DECLARE
 inst_record dbms_utility.instance_record;
 inst_tab dbms_utility.instance_table;
 inst_cnt NUMBER;
BEGIN
    dbms_utility.active_instances(inst_tab, inst_cnt);
    for i in 1..inst_cnt loop
        DBMS_OUTPUT.put_line(inst_tab(i).inst_number || ' <=> ' || inst_tab(i).inst_name);
    end loop;
END;
/

---result
1 <=> yasdb
```

## COMMA_TO_TABLE

```plsql
TYPE LNAME_ARRAY IS TABLE of VARCHAR(65534) INDEX BY BINARY_INTEGER;
TYPE UNCL_ARRAY IS TABLE OF VARCHAR(65534) INDEX BY BINARY_INTEGER;

DBMS_UTILITY.COMMA_TO_TABLE ( 
   list   IN  VARCHAR2,
   tablen OUT BINARY_INTEGER,
   tab    OUT uncl_array); 

DBMS_UTILITY.COMMA_TO_TABLE ( 
   list   IN  VARCHAR2,
   tablen OUT BINARY_INTEGER,
   tab    OUT lname_array);
```

The COMMA_TO_TABLE procedure is used to convert a comma-separated name string into an array. This procedure is not applicable to ISC Distributed Cluster Deployment.

|Parameter |Description |
| :--- | :---- |
| list             | A comma-separated 'name' list string. The name string supports the following forms based on the tab type: <br/> \* If tab is of type UNCL_ARRAY, the name string format is `identifier[.identifier[.identifier]]@dblink` <br/> \* If tab is of type LNAME_ARRAY, the name string format is `identifier[.identifier]*` |
| tablen           | The number of split names                      |
| tab              | The associative array obtained from converting the name list string |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    l_list    VARCHAR(4000) := '  c  ,     a.b.c    , dd@dd@"k"  ';
    l_count   BINARY_INTEGER;
    l_array   DBMS_UTILITY.UNCL_ARRAY;
BEGIN
    -- Convert comma-separated string to table
    DBMS_UTILITY.COMMA_TO_TABLE(list => l_list, tablen => l_count, tab => l_array);
    
    -- Output the number of elements in the converted table
    DBMS_OUTPUT.PUT_LINE('Number of elements: ' || TO_CHAR(l_count));
	DBMS_OUTPUT.PUT_LINE('1:' || l_array(1) || 'end' );
	DBMS_OUTPUT.PUT_LINE('2:' || l_array(2) || 'end');
    DBMS_OUTPUT.PUT_LINE('3:' || l_array(3) || 'end' );
    
END;
/

--result
Number of elements: 3
1:  c  end
2:     a.b.c    end
3: dd@dd@"k"  end
```

## CURRENT_INSTANCE

```plsql
DBMS_UTILITY.CURRENT_INSTANCE()
   RETURN NUMBER;
```

The CURRENT_INSTANCE function is used to obtain the instance number of the current connection.

***Example***

```plsql
select dbms_utility.current_instance() from dual;

CURRENT_INSTANCE
----------------
           1
```

## DB_VERSION

```plsql
dbms_utility.db_version (
version       OUT VARCHAR2,
compatibility OUT VARCHAR2);
```

The DB_VERSION procedure is used to obtain the database version information.

|Parameter |Description |
| :--- | :---- |
| version          | A string representing the internal software version of the database |
| compatibility     | A placeholder parameter used only for compatibility; parameter value has no actual effect. |

***Example***

```plsql
set serveroutput on
DECLARE
 ver    char(16);
 compat char(12);
BEGIN
  dbms_utility.db_version(ver, compat);
  dbms_output.put_line('Version: ' || ver ||' Compatible: ' || compat);
END;
/

--- result
Version: 23.4.1.100        Compatible:
```

## EXEC_DDL_STATEMENT

```plsql
DBMS_UTILITY.EXEC_DDL_STATEMENT (
  parse_string IN VARCHAR2
  );
```

The EXEC_DDL_STATEMENT procedure is used to execute DDL statements. This procedure is not applicable to ISC Distributed Cluster Deployment.

|Parameter |Description |
| :--- | :---- |
| parse_string     | The statement to be executed                   |

***Example***
```plsql
DECLARE
BEGIN
    DBMS_UTILITY.EXEC_DDL_STATEMENT('CREATE TABLE UTILITY_TABLE(A INT, B INT)');
END;
/
SELECT A,B FROM UTILITY_TABLE;

--result
           A            B
------------ ------------

0 rows fetched.
```

## FORMAT_CALL_STACK

```plsql
DBMS_UTILITY.FORMAT_CALL_STACK() 
  RETURN VARCHAR;
```

The FORMAT_CALL_STACK function returns a formatted output of the current procedure call stack with no parameters. The output can be up to 2000 bytes.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE PROC_CALL AS
BEGIN
DBMS_OUTPUT.PUT_LINE(DBMS_UTILITY.FORMAT_CALL_STACK);
END;
/

CALL PROC_CALL;

--result
----- PL/SQL Call Stack ----
object handle     line number  object  name
0x7fffe2e39010              3  procedure  REGRESS.PROC_CALL
0x7fffdfb3bd90              1  anonymous block  
```

## FORMAT_ERROR_BACKTRACE

```plsql
DBMS_UTILITY.FORMAT_ERROR_BACKTRACE()
  RETURN VARCHAR2;
```

The FORMAT_ERROR_BACKTRACE function displays the location where an exception was raised, with a maximum length of 4000 bytes.

***Example***

```plsql
set serveroutput on
CREATE OR REPLACE PROCEDURE format_error_backtrace_p0 IS
   str varchar(4000);
BEGIN
    str := 1/0;
   DBMS_OUTPUT.PUT_LINE('Hello, this is my first stored procedure!');
END format_error_backtrace_p0;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p1 IS
BEGIN
  format_error_backtrace_p0();
END format_error_backtrace_p1;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p2 IS
BEGIN
  format_error_backtrace_p1();
END format_error_backtrace_p2;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p3 IS
BEGIN
  format_error_backtrace_p2();
END format_error_backtrace_p3;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p4 IS
BEGIN
  format_error_backtrace_p3();
END format_error_backtrace_p4;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p5 IS
BEGIN

  format_error_backtrace_p4();
END format_error_backtrace_p5;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_top_nolog IS
BEGIN
  format_error_backtrace_p5();
END format_error_backtrace_top_nolog;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_top_logging IS
BEGIN
  format_error_backtrace_p5();
exception
when others then
    dbms_output.put_line('Error_Backtrace...' || CHR(10) ||
    dbms_utility.format_error_backtrace());
    dbms_output.put_line( '----------' );
END format_error_backtrace_top_logging;
/

exec format_error_backtrace_top_logging;

--- result 
Error_Backtrace...
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P0", line 4
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P1", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P2", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P3", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P4", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P5", line 4
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_TOP_LOGGING", line 3

----------
```

## FORMAT_ERROR_STACK

```plsql
DBMS_UTILITY.FORMAT_ERROR_STACK()
  RETURN VARCHAR;
```

The FORMAT_ERROR_STACK function returns a formatted output of the current error stack with no parameters, and the output can be up to 2000 bytes.

***Example***

```plsql
DECLARE
N INT;
BEGIN
N := 10 / 0;
EXCEPTION
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(DBMS_UTILITY.FORMAT_ERROR_STACK);
END;
/

--result
YAS-00011 divided by zero
```

## GET_ENDIANNESS

```plsql
DBMS_UTILITY.GET_ENDIANNESS()
  RETURN NUMBER;
```

The GET_ENDIANNESS function is used to obtain the byte order of the current database platform. A return value of 1 indicates big-endian byte order, while a return value of 2 indicates little-endian byte order.

***Example***
```plsql
select DBMS_UTILITY.GET_ENDIANNESS() from dual;
```

## GET_HASH_VALUE

```plsql
DBMS_UTILITY.GET_HASH_VALUE (
  NAME       IN  VARCHAR, 
  BASE       IN  NUMBER, 
  HASH_SIZE  IN  NUMBER)
  RETURN NUMBER;
```

The GET_HASH_VALUE function computes a hash value for a string falling within a specified range, which is `[BASE, BASE + HASH_SIZE - 1]`.

|Parameter |Description |
| :--- | :---- |
| NAME             | The string for which the hash value needs to be computed; cannot be NULL or an empty string. |
| BASE             | The starting value for the hash.              |
| HASH_SIZE        | The size of the hash.                          |

Usage notes:

- The range of BASE and HASH_SIZE values is \[-2<sup>31</sup>, 2<sup>31</sup> - 1\].
- If BASE is a floating-point number, it will be rounded to the nearest integer.
- HASH_SIZE must not be 0 or a floating-point number.

***Example***

```plsql
select DBMS_UTILITY.GET_HASH_VALUE('abc', 1000, 2048) from dual;
```

## GET_PARAMETER_VALUE

```plsql
DBMS_UTILITY.GET_PARAMETER_VALUE (
   parnam     IN        VARCHAR2,
   intval     IN OUT    BINARY_INTEGER,
   strval     IN OUT    VARCHAR2,
   listno     IN        BINARY_INTEGER DEFAULT 1)
  RETURN BINARY_INTEGER;
```

The GET_PARAMETER_VALUE function retrieves the value of a system parameter, which can be found in V$PARAMETER.

If the parameter type is INTEGER/BOOL, the return value is 0. If the type is VARCHAR, the return value is 1.

|Parameter |Description |
| :--- | :---- |
| param            | The parameter name                             |
| intval           | If the parameter value is of type INTEGER/BOOL, returns the corresponding value. If the parameter value is of type string, returns the length of the corresponding value. |
| strval           | If the parameter value is of type INTEGER/BOOL, the return value is NULL. If the parameter value is of type string, returns the corresponding value. |
| listno           | A placeholder parameter used only for compatibility; parameter value has no actual effect. |

***Example***

```plsql
DECLARE
  -- Parameter name, integer value, string value, and parameter type
  parnam  VARCHAR2(256) := 'DSINTERVAL_FORMAT';  -- Parameter name
  intval  BINARY_INTEGER;  -- To store integer parameter value or length of string parameter value
  strval  VARCHAR2(256);  -- To store string parameter value
  partyp  BINARY_INTEGER;  -- Parameter type identifier
BEGIN
  -- Retrieve parameter value and type using GET_PARAMETER_VALUE function
  partyp := DBMS_UTILITY.GET_PARAMETER_VALUE(parnam, intval, strval);
  
  -- Output the parameter value
  DBMS_OUTPUT.PUT('Parameter value is: ');
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT_LINE(strval);  -- Output string value if it is a string parameter
  ELSE
    DBMS_OUTPUT.PUT_LINE(TO_CHAR(intval));  -- Output integer value if it is an integer parameter
  END IF;

  -- If it is a string parameter, also output the length of the string
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT('Parameter value length is: ');
    DBMS_OUTPUT.PUT_LINE(intval);  -- Output length of the string
  END IF;

  -- Output the parameter type (integer or string)
  DBMS_OUTPUT.PUT('Parameter type is: ');
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT_LINE('string');
  ELSE
    DBMS_OUTPUT.PUT_LINE('integer');
  END IF;
END;
/

--result
Parameter value is: dd hh24:mi:ss.ff
Parameter value length is: 16
Parameter type is: string
```

## GET_SQL_HASH

```plsql
DBMS_UTILITY.GET_SQL_HASH (
   name          IN   VARCHAR2,
   hash          OUT  RAW,
   pre10ihash    OUT  NUMBER)
  RETURN NUMBER;
```

The GET_SQL_HASH function computes the hash value of a given string using the MD5 algorithm.

|Parameter |Description |
| :--- | :---- |
| name             | The string to be hashed                      |
| hash             | An optional field to store the returned 16-byte hash value. |
| pre10ihash      | This parameter is unsupported; the return result is NULL. |

***Example***

```plsql
set serveroutput on

DECLARE
 h Varchar(32);
 n NUMBER;
 x NUMBER;
BEGIN
  x :=  dbms_utility.get_sql_hash('select 1 from t1', h, n);

  dbms_output.put_line('Return Value: ' || x);
  dbms_output.put_line('Hash: ' || h);
  dbms_output.put_line('Pre10iHash: ' || TO_CHAR(n));
END;
/

--- result
Return Value: 1334230816
Hash: 1E7E2F21208F80F4BEB552F320BF864F
Pre10iHash: 
```

## GET_TIME

```plsql
DBMS_UTILITY.GET_TIME()
  RETURN NUMBER;
```

The GET_TIME function returns a numeric value (local SCN) representing the current time in hundredths of a second.

This value is related to the current system time and may be negative.

***Example***
```plsql
-- Get current time
select DBMS_UTILITY.GET_TIME() from dual;

-- Output 200hsec = 2 sec
set serveroutput on;
DECLARE
  start_time NUMBER;
  end_time NUMBER;
BEGIN
  start_time:= DBMS_UTILITY.GET_TIME();
  DBMS_LOCK.SLEEP(2);
  end_time:= DBMS_UTILITY.GET_TIME() - start_time;
  DBMS_OUTPUT.PUT_LINE('elapsed = ' || end_time || 'hsecs');
END;
/
set serveroutput off;
```

## IS_BIT_SET

```plsql
DBMS_UTILITY.IS_BIT_SET (
  R     IN    RAW,
  N     IN    NUMBER)
  RETURN NUMBER;
```

The IS_BIT_SET function checks whether the bit corresponding to the given position in the RAW variable is set.

|Parameter |Description |
| :--- | :---- |
| R               | The RAW variable to be checked                |
| N               | The position to be checked; N must be a positive integer and less than the bit length of the RAW variable. |

***Example***

```plsql
SELECT DBMS_UTILITY.IS_BIT_SET(HEXTORAW('AB'), 1) FROM DUAL;
```

## IS_CLUSTER_DATABASE

```plsql
DBMS_UTILITY.IS_CLUSTER_DATABASE()
  RETURN BOOLEAN;
```

The IS_CLUSTER_DATABASE function determines if the database is in YAC/Distributed Cluster Deployment, returning TRUE if it is, otherwise FALSE.

***Example***

```plsql
set serveroutput on;
BEGIN
  IF DBMS_UTILITY.IS_CLUSTER_DATABASE THEN
    DBMS_OUTPUT.PUT_LINE('TRUE');
  ELSE
    DBMS_OUTPUT.PUT_LINE('FALSE');
  END IF;
END;
/
set serveroutput off;
```

## NAME_RESOLVE

```plsql
DBMS_UTILITY.NAME_RESOLVE (
   name          IN  VARCHAR, 
   context       IN  NUMBER,
   schema        OUT VARCHAR, 
   part1         OUT VARCHAR, 
   part2         OUT VARCHAR,
   dblink        OUT VARCHAR, 
   part1_type    OUT NUMBER, 
   object_number OUT NUMBER);
```

This procedure's input form is equivalent to the NAME_TOKENIZE procedure. This procedure attempts to parse the name into four parts: a, b, c, and dblink, confirming the true meanings of a, b, c, and dblink based on the specified context value.

|Parameter |Description |
| :--- |:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name             | Object name in format [[a.]b.]c[@d], where a, b, c are SQL objects and d is a dblink. |
| context          | An integer in the range [0,9] specifying the parsing mode: <br> \* 0 - table: table <br/> \* 1 - function/procedure/package: function/procedure/package <br/> \* 2 - sequence: sequence <br/> \* 3 - trigger: trigger <br/> \* 7 - type: type <br/> \* 9 - index: index |
| schema           | The schema of the object; if not specified, the name is determined by the object's owning schema. |
| part1            | The first part of the name; this name's type is specified by part1_type (synonym or package). |
| part2            | If non-NULL, indicates the subprogram name. If part1 is non-NULL, this indicates the subprogram is within the package referred to by part1; if part1 is NULL, the subprogram is a top-level subprogram. |
| dblink           | If non-NULL, indicates that a database link is specified as part of the name, or that the name is a synonym which resolves to something with a database link. In this case, to further resolve the name (i.e., invoke NAME_RESOLVE on a remote node), the NAME_RESOLVE procedure will need to be called on the remote node. |
| part1_type       | The [type](../../../Reference Manual/System Views/DBA Views/DBA_OBJECTS) of the first part. |
| object_number     | The object ID.                                  |

Usage rules:

- When the object name [[a.]b.]c[@d] has a non-empty dblink, NAME_RESOLVE degrades to NAME_TOKENIZE, only parsing and outputting the schema, part1, part2, and dblink.
- When the object name [[a.]b.]c[@d] has an empty dblink, the schema, and objectName will be parsed from a, b, and c. The c part is only effective when context is 1 for package objects.
- When the object name [[a.]b.]c[@d] has only a non-empty a, then a is objectName, and schema is the current username. If the object name [[a.]b.]c[@d] has both a and b non-empty, then a is the schema, b is the objectName. The corresponding object oid will then be fetched based on the context value of c, and the corresponding oid will be returned via the object_number parameter and the part1_type parameter will return the corresponding object type; an error will be raised if the corresponding object type cannot be fetched.

***Example***

```plsql
-- Using the area table of the sales user as an example
DECLARE
   l_schema VARCHAR2(30);
   l_part1 VARCHAR2(30);
   l_part2 VARCHAR2(30);
   l_dblink VARCHAR2(30);
   l_part1_type NUMBER;
   l_object_number NUMBER;
BEGIN
   DBMS_UTILITY.NAME_RESOLVE('area', 0, l_schema, l_part1, l_part2, l_dblink, l_part1_type, l_object_number);
   DBMS_OUTPUT.PUT_LINE('Schema: ' || l_schema);
   DBMS_OUTPUT.PUT_LINE('Part1: ' || l_part1);
   DBMS_OUTPUT.PUT_LINE('Part2: ' || l_part2);
   DBMS_OUTPUT.PUT_LINE('DB Link: ' || l_dblink);
   DBMS_OUTPUT.PUT_LINE('Part1 Type: ' || l_part1_type);
   DBMS_OUTPUT.PUT_LINE('Object Number: ' || l_object_number);
END;
/

--result
Schema: SALES
Part1: AREA
Part2:
DB Link:
Part1 Type: 1
Object Number: 2426
```

## NAME_TOKENIZE

```plsql
DBMS_UTILITY.NAME_TOKENIZE ( 
   name    IN  VARCHAR,
   a       OUT VARCHAR,
   b       OUT VARCHAR,
   c       OUT VARCHAR,
   dblink  OUT VARCHAR, 
   nextpos OUT BINARY_INTEGER);
```

The NAME_TOKENIZE procedure uses a parser to output the input name in the form of `identifier[.identifier[.identifier]][@dblink]` into a, b, c, and dblink, where the dblink takes the form of `identifier[.identifier]*[@identifier]`. Non-quoted **identifier** will be converted to uppercase.

|Parameter |Description |
| :--- | :---- |
| name             | A SQL identifier, e.g., `scott.foo@dblink`   |
| a                | The first token part                          |
| b                | The second token part                         |
| c                | The third token part                          |
| dblink           | The part after @ dblink                       |
| nextpos         | The string index where parsing stops, e.g., for `a.b c`, nextpos is 3 |

***Example***
```plsql
DECLARE
   name VARCHAR(20000) := 'a.d.c@rdb';
   a VARCHAR(2000);
   b VARCHAR(2000);
   c VARCHAR(2000);
   dblink VARCHAR(2000);
   nextpos BINARY_INTEGER;
BEGIN
   DBMS_UTILITY.NAME_TOKENIZE(
      name => name,
      a => a,
      b => b,
      c => c,
      dblink => dblink,
      nextpos => nextpos
   );
   DBMS_OUTPUT.PUT_LINE('A: ' || a);
   DBMS_OUTPUT.PUT_LINE('B: ' || b);
   DBMS_OUTPUT.PUT_LINE('C: ' || c);
   DBMS_OUTPUT.PUT_LINE('DBLink: ' || dblink);
   DBMS_OUTPUT.PUT_LINE('Nextpos: ' || nextpos);
END;
/

--result
A: A
B: D
C: C
DBLink: RDB
Nextpos: 9
```

## OLD_CURRENT_SCHEMA

```plsql
DBMS_UTILITY.OLD_CURRENT_SCHEMA()
 RETURN VARCHAR;
```

The OLD_CURRENT_SCHEMA function returns the schema of the current session.

***Example***

```plsql
select DBMS_UTILITY.OLD_CURRENT_SCHEMA() from dual;
```

## OLD_CURRENT_USER

```plsql
DBMS_UTILITY.OLD_CURRENT_USER()
 RETURN VARCHAR;
```

The OLD_CURRENT_USER function returns the user of the current session.

***Example***

```plsql
select DBMS_UTILITY.OLD_CURRENT_USER() from dual;
```

## PORT_STRING

```plsql
DBMS_UTILITY.PORT_STRING()
  RETURN VARCHAR;
```

The PORT_STRING function returns the operating system platform and version.

***Example***

```plsql
select DBMS_UTILITY.PORT_STRING() from dual;
```

## TABLE_TO_COMMA

```plsql
DBMS_UTILITY.TABLE_TO_COMMA ( 
   tab    IN  UNCL_ARRAY, 
   tablen OUT BINARY_INTEGER,
   list   OUT VARCHAR2);

DBMS_UTILITY.TABLE_TO_COMMA ( 
   tab    IN  LNAME_ARRAY,
   tablen OUT BINARY_INTEGER,
   list   OUT VARCHAR2);
```

The TABLE_TO_COMMA procedure converts an UNCL_ARRAY or LNAME_ARRAY type name array into a comma-separated string. This procedure is not applicable to ISC Distributed Cluster Deployment.

The procedure does not validate the input of tab.

|Parameter |Description |
| :--- | :---- |
| tab              | The associative array of 'name'               |
| tablen           | The length of the array                        |
| list             | The returned comma-separated 'name' string     |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
   -- Use DBMS_UTILITY.LNAME_ARRAY type
   myArray DBMS_UTILITY.UNCL_ARRAY;
   
   -- Used to store the converted string and array length
   listOut VARCHAR(4000);
   tabLen  BINARY_INTEGER;
BEGIN
   -- Add data to the array
   myArray(1) := '++++ ++++          +++         +++ ';
   myArray(2) := '1111111100000000000001111111111111111';

   -- Call TABLE_TO_COMMA procedure
   DBMS_UTILITY.TABLE_TO_COMMA(
      tab    => myArray, 
      tablen => tabLen,
      list   => listOut
   );

   -- Output the result
   DBMS_OUTPUT.PUT_LINE('Comma-separated list: ' || listOut);
   DBMS_OUTPUT.PUT_LINE('Number of elements: ' || tabLen);
END;
/

--result
iComma-separated list: ++++ ++++          +++         +++ ,1111111100000000000001111111111111111
Number of elements: 2

```
