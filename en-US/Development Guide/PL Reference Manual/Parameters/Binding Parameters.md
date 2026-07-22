As mentioned on the homepage of this chapter, bind parameters are used in dynamic SQL, where placeholders correspond to the formal parameters in stored procedures/functions, and the values or variables specified by the USING syntax correspond to the actual parameters in stored procedures/functions. The differences are:

1. The placeholders representing formal parameters cannot define parameter types, default values, and other information. The parameter types of formal parameters are determined by the values or variables passed as actual parameters.
2. Actual parameters can only directly input values or variables, corresponding one-to-one with formal parameters by position, with no association to placeholder names.
3. Unlike the specification of IN/OUT/IN OUT types for formal parameters in stored procedures/functions, bind parameters are specified as actual parameters only when passing variables, and only when the dynamic SQL statement has a return value can it be specified as OUT/IN OUT; otherwise, it defaults to the IN type.
4. Since '' and NULL have no data type, the value of actual parameters cannot be '' and NULL.

## Placeholders

A placeholder consists of `:` + identifier, and it's recommended that the identifier use numbers or letters.

When passing actual parameters in the same SQL statement, they correspond to the placeholders based on their positions. If different values are bound to the SQL statement, different placeholder names should be used; otherwise, a compilation error will occur.

***Example***

```plsql
-- Define a stored procedure where the a2 parameter is assigned a value in the procedure body
CREATE OR REPLACE PROCEDURE ya_block(a1 VARCHAR,a2 IN OUT VARCHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no in (:a,:b) and rownum=1';
BEGIN
-- There is no return value in str1, thus the bind variables a1, a2 cannot be specified as OUT/IN OUT for parameter passing
EXECUTE IMMEDIATE str1 INTO no,name USING a1,a2;
DBMS_OUTPUT.PUT_LINE('Area Number: '||no||'Area Name: '||name);
a2 := '00';
END;
/
 
DECLARE
a1 VARCHAR(20):='01';
a2 VARCHAR(20):='02';
str2 VARCHAR(200) := 'INSERT INTO area(area_no) VALUES(:a) RETURN TO_NUMBER(area_no), NVL(area_name, ''area''||area_no) INTO :b, :c';
BEGIN
DBMS_OUTPUT.PUT_LINE('before-a1:'||a1||',a2:'||a2);
-- The a2 parameter of ya_block has a return value and can be defined as OUT/IN OUT type
EXECUTE IMMEDIATE 'begin ya_block(:b,:c); end;' USING a1,IN OUT a2;
DBMS_OUTPUT.PUT_LINE('plsql.after-a1:'||a1||',a2:'||a2);

-- In str2, a RETURN value is defined, so the bind variables a1, a2 can be specified as OUT/IN OUT type for parameter passing
EXECUTE IMMEDIATE str2 USING '09',out a1,out a2;
DBMS_OUTPUT.PUT_LINE('sql.after-a1:'||a1||',a2:'||a2);
END;
/

--result
before-a1:01,a2:02
Area Number: 01 Area Name: EastChina
plsql.after-a1:01,a2:00
sql.after-a1:9,a2:area09
```

When modifying the object of USING through bind parameters, the modification only takes effect internally during the execution of dynamic SQL. It will only assign the value to the original object after the execution of dynamic SQL is finished.

***Example***
```plsql
CREATE OR REPLACE PACKAGE area_pkg IS
area_name VARCHAR2(60) := 'SouthChina';
END;
/

DECLARE
num VARCHAR(2) := '1';
a_name VARCHAR(20):='SHENZHEN';
BEGIN
-- Print before-area_pkg.area_name: SouthChina
DBMS_OUTPUT.PUT_LINE('before-area_pkg.area_name: '|| area_pkg.area_name);

-- Dynamically modify area_pkg.area_name using bind parameters internally and print directly by full name
-- Modification occurs only after dynamic SQL ends, thus printing in dynamic SQL: SouthChina
EXECUTE IMMEDIATE 'begin 
INSERT INTO area(area_no, area_name) VALUES(:1, :2) RETURN area_name INTO :3; 
DBMS_OUTPUT.PUT_LINE(''in dynamic SQL: ''|| area_pkg.area_name);
end;' USING num, a_name, IN OUT area_pkg.area_name;

-- Print after-area_pkg.area_name: SHENZHEN
DBMS_OUTPUT.PUT_LINE('after-area_pkg.area_name: '|| area_pkg.area_name);
END;
/
```

## Variable Peeking

When using bind parameters, the YashanDB optimizer can merge similar SQL statements into a single execution plan, avoiding multiple hard parses.

Variable peeking is a further optimization on this functionality, achieving:

1. Inferring the data types of placeholders in the SQL statement based on the data types of actual parameters.
2. Performing dynamic peeking through the bound variables passed as actual parameters to generate the most suitable execution plan, rather than just using the execution plan generated based on a single bound variable.

The variable peeking functionality in YashanDB is not perceived by users and is automatically invoked when using bind parameters in dynamic SELECT statements, enhancing system performance.

Starting from version 23.1, the support for variable peeking introduced some phenomena that differ from those in the version prior to 23.1 when inferring types in certain scenarios involving bind variables.

***Example***

```plsql
CREATE TABLE t1 (id INT, data CHAR(255));
INSERT INTO t1 VALUES(1, 'test');
COMMIT;
set serveroutput on;

declare
   var varchar(100) := 'test';
   count int;
begin
   select count(*) into count from t1 where data = var;
   DBMS_OUTPUT.PUT_LINE(count);
end;
/
```

In this anonymous block, line 5 uses a bind variable to pass the var variable into the SQL statement for execution.

In versions before 23.1, without variable peeking, an unknown variable to be bound would infer a type from the most recent operand data type. In this example, var would be inferred to be of the same type as data, which is CHAR. When the var variable is passed in, it would be converted to CHAR type for comparison with data. At this point, the comparison rule is between CHAR type and CHAR type, where trailing spaces are insensitive, as detailed in [Character Types](../../SQL Reference Manual/Data Types/Character Types). Therefore, the WHERE condition matches successfully, resulting in 1.

In version 23.1 and later, with variable peeking, the real data type of var is passed in, leading to a comparison of data as CHAR type and var as VARCHAR type, where trailing spaces are sensitive, as detailed in [Character Types](../../SQL Reference Manual/Data Types/Character Types). Therefore, the WHERE condition does not match successfully, resulting in 0.