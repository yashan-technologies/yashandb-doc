SUBTYPE is a subset of a data type, derived from a specific data type (base type). Subtypes inherit the same data type and descriptive information from the base type and can also support additional unique scope and constraint information.

## SUBTYPE Syntax

When defining a user-defined SUBTYPE, you must explicitly specify the name of the SUBTYPE, the data type, and constraints.

The definition syntax is as follows:

```ebnf+diagram
syntax::= SUBTYPE subtype_name IS base_type [constraint]
```

The constraint syntax is as follows:
```ebnf+diagram
syntax::= { "(" precision ["," scale] ")" | RANGE low_value ".." high_value} [ [NOT] NULL ]
```

**base_type**

- base_type refers to all supported data types in the database (scalar types, UDT, SUBTYPE).
- base_type can be inherited types of %TYPE and %ROWTYPE.
- Refer to [Data Types](../../../SQL Reference Manual/Data Types (yashan Mode)/00Data Types (yashan Mode)), [UDT](../../PL Objects/User-Defined Types), and [Reference Variables](Reference Variables).

**constraint**

- constraint specifies additional information besides the precision and SIZE of the data type, and it can support RANGE to specify value ranges (supporting constant expression operations).
- For specific precision, SIZE, and other information, refer to [Data Types](../../../SQL Reference Manual/Data Types (yashan Mode)/00Data Types (yashan Mode)).

## SUBTYPE Usage

### Inheriting Scalars

When base_type is a standard scalar.

***Example***

```plsql
DECLARE
SUBTYPE t1 IS TINYINT;
SUBTYPE t2 IS SMALLINT;
SUBTYPE t3 IS INT;
SUBTYPE t4 IS BIGINT;
SUBTYPE t5 IS FLOAT;
SUBTYPE t6 IS DOUBLE;
SUBTYPE t7 IS NUMBER;
SUBTYPE t8 IS BIT;
v1 t1 := 127;
v2 t2 := 2 * (v1 + 1) * (v1 + 1) - 1;
v3 t3 := 2 * (v2 + 1) * (v2 + 1) - 1;
v4 t4 := 2 * (v3 + 1) * (v3 + 1) - 1;
v5 t5 := 3.402823E38;
v6 t6 := 1.79769313486232E307;
v7 t7 := 1E126;
v8 t8 := 1;
BEGIN
dbms_output.put_line(v1);
dbms_output.put_line(v2);
dbms_output.put_line(v3);
dbms_output.put_line(v4);
dbms_output.put_line(v5);
dbms_output.put_line(v6);
dbms_output.put_line(v7);
dbms_output.put_line(v8);
END;
/

--result
127
32767
2147483647
9223372036854775807
3.40282306E+038
1.79769313486232E+307
9.999999999999999200000000000000000E+125
1
```

### Inheriting Complex Types

When base_type is a UDT or SUBTYPE.

***Example***

```plsql

CREATE OR REPLACE TYPE obj AS OBJECT(c1 INT, c2 VARCHAR(4));
/
CREATE OR REPLACE TYPE arr AS VARRAY(5) OF CHAR(4);
/
CREATE OR REPLACE TYPE tb IS TABLE OF CHAR(4);
/
CREATE OR replace PACKAGE pkg1 AS
v1 VARCHAR(5);
subtype t is varchar(5);
end;
/

DECLARE
SUBTYPE t1 IS obj;
SUBTYPE t2 IS arr;
SUBTYPE t3 IS tb;
SUBTYPE t4 IS pkg1.t;
SUBTYPE t5 IS pkg1.v1%TYPE;

v1 t1 := obj(2000, 'a');
v2 t2 := arr(2000, 'a');
v3 t3 := tb(2000, 'a');
v4 t4 := '2000a';
v5 t5 := '2000a';

BEGIN
dbms_output.put_line(v1.c1 || v1.c2);
dbms_output.put_line(v2(1) || v2(2));
dbms_output.put_line(v3(1) || v3(2));
dbms_output.put_line(v4);
dbms_output.put_line(v5);
END;
/

--result
2000a
2000a
2000a
2000a
2000a
```
