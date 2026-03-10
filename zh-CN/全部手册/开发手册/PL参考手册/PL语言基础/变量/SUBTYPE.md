SUBTYPE是一种数据类型的子集，即根据某一种数据类型（基类型）继承出来的子类型。子类型会从基类型继承相同的数据类型、描述信息，也支持追加独属于子类型的有效范围和约束信息。

## SUBTYPE语法

用户自定义SUBTYPE需要显式指定SUBTYPE的名称、数据类型及约束等。

定义语法如下：

```ebnf+diagram
syntax::= SUBTYPE subtype_name IS base_type [constraint]
```

constraint语法如下：
```ebnf+diagram
syntax::= { "(" precision ["," scale] ")" | RANGE low_value ".." high_value} [ [NOT] NULL ]
```

**base_type**

- base_type是数据库中所有已支持的数据类型（标量类型、UDT、SUBTYPE）。
- base_type可以是%TYPE、%ROWTYPE的继承类型。
- 可参考[数据类型](../../../SQL参考手册/数据类型（yashan模式）/00数据类型（yashan模式）)、[自定义类型](../../PL对象/自定义类型)和[引用变量](引用变量)。

**constraint**

- constraint 额外指定的信息除了数据类型本身的精度、SIZE信息，可以支持RANGE指定值范围（支持常量表达式运算）。
- 具体精度、SIZE等信息可参考[数据类型](../../../SQL参考手册/数据类型（yashan模式）/00数据类型（yashan模式）)。

## SUBTYPE使用

### 继承标量

base_type为普通标量。

示例

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

### 继承复杂类型

base_type为UDT或SUBTYPE。

示例

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
