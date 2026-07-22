DBMS_SQL包提供了一组内置接口，用于执行动态SQL，包括DDL和DML。

## OPEN_CURSOR

```plsql
DBMS_SQL.OPEN_CURSOR (
   security_level                 IN     INTEGER    DEFAULT 1,
   treat_as_client_for_results    IN     BOOLEAN    DEFAULT FALSE) 
  RETURN INTEGER;
```

OPEN_CURSOR用于打开一个新游标，返回游标的ID值，返回值为INTEGER类型。

|  参数| 描述|
| :---- |:-----------|
| security_level | 安全级别，INTEGER类型。默认值为1，其他合法值为语法兼容。|
| treat_as_client_for_results | 语法兼容。 |

说明：

- 打开的游标为session级别资源，主动关闭或session断连时释放游标资源。
- 游标可以重复执行SQL语句或解析新SQL语句，无需关闭再重新打开游标。
- 暂只支持安全级别1，在绑定变量和执行阶段检查用户是否与解析阶段相同。

示例

```plsql
DECLARE
  C INTEGER;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.
```

## CLOSE_CURSOR

```plsql
DBMS_SQL.CLOSE_CURSOR (
   c    IN OUT INTEGER);
```

CLOSE_CURSOR用于关闭一个给定游标，释放相应的游标资源。

|  参数| 描述|
| :---- |:-----------|
| c | 需要释放的游标ID，INTEGER类型。<br/>执行成功后可作为输出参数，并将被自动赋值为NULL。 |

示例

```plsql
DECLARE
  C INTEGER;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.
```

## PARSE

```plsql
DBMS_SQL.PARSE (
   c                           IN   INTEGER,
   statement                   IN   VARCHAR2,
   language_flag               IN   INTEGER[
 [,edition                     IN   VARCHAR2 DEFAULT NULL],
   apply_crossedition_trigger  IN   VARCHAR2 DEFAULT NULL,
   fire_apply_trigger          IN   BOOLEAN DEFAULT TRUE]
 [,schema                      IN   VARCHAR2 DEFAULT NULL]
 [,container                   IN   VARCHAR2)];
```

或：

```plsql
DBMS_SQL.PARSE (
   c                           IN   INTEGER,
   statement                   IN   CLOB,
   language_flag               IN   INTEGER[
 [,edition                     IN   VARCHAR2 DEFAULT NULL],
   apply_crossedition_trigger  IN   VARCHAR2 DEFAULT NULL,
   fire_apply_trigger          IN   BOOLEAN DEFAULT TRUE]
 [,schema                      IN   VARCHAR2 DEFAULT NULL]
 [,container                   IN   VARCHAR2)];  
```

PARSE使用给定的游标，解析给定的SQL语句。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| statement | 语法解析的SQL语句，VARCHAR类型或CLOB类型。<br/>若SQL语句的长度大于32K，则需使用CLOB类型。|
| language_flag | 合法值范围0-6，常用值为常量DBMS_SQL.NATIVE。语法兼容。 |
| edition | 语法兼容。 |
| apply_crossedition_trigger | 语法兼容。 |
| fire_apply_trigger | 语法兼容。 |
| schema | 指定解析非限定对象名称的SCHEMA，如果为NULL，则为当前的SCHEMA。|
| container | 语法兼容。 |

说明：

- SQL语句最大长度不能超过2M。
- DDL语句会在PARSE过程中立即执行。

示例

```plsql
DECLARE
  C INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.
```

## BIND_VARIABLE

```plsql
DBMS_SQL.BIND_VARIABLE (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN <datatype>);
```

或：

```plsql
DBMS_SQL.BIND_VARIABLE (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN VARCHAR2 [,out_value_size IN INTEGER]);
```

BIND_VARIABLE根据语句中绑定变量名称将值绑定到绑定变量内。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 绑定参数名称，VARCHAR类型。需要与语句内的绑定变量名称匹配。|
| value | 绑定到绑定变量的值，可为SQL语句支持的任一类型。|
| out_value_size | 指定VARCHAR类型的出参值大小，INTEGER类型。|

说明：

- name参数中的前导冒号可以省略。
- 如果指定了out_value_size，则视作value为VARCHAR类型。

示例
```plsql
INSERT INTO department VALUES('111', 'R&D Dep');

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM department WHERE department_name = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE(C, ':B1', 'R&D Dep');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.

DELETE FROM department WHERE deparment_no='111';
```

## BIND_VARIABLE_RAW

```plsql
DBMS_SQL.BIND_VARIABLE_RAW (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN RAW [,out_value_size IN INTEGER]);
```

BIND_VARIABLE_RAW用于绑定RAW类型值的绑定变量。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 绑定参数名称，VARCHAR类型。需要与语句内的绑定变量名称匹配。|
| value | 绑定到绑定变量的值，RAW类型。|
| out_value_size | 指定RAW类型的出参值大小，INTEGER类型。|

示例
```plsql
CREATE TABLE bind_raw(c1 int, c2 RAW(100));
INSERT INTO bind_raw VALUES(1, '1');
COMMIT;

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM bind_raw WHERE C2 = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_RAW(C, ':B1', '1');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.

DROP TABLE bind_raw;
```

## BIND_VARIABLE_CHAR

```plsql
DBMS_SQL.BIND_VARIABLE_CHAR (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN CHAR [,out_value_size IN INTEGER]);
```

BIND_VARIABLE_CHAR用于绑定CHAR类型值的绑定变量。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 绑定参数名称，VARCHAR类型。需要与语句内的绑定变量名称匹配。|
| value | 绑定到绑定变量的值，CHAR类型。|
| out_value_size | 指定RAW类型的出参值大小，INTEGER类型。|

示例
```plsql
CREATE TABLE bind_char(c1 int, c2 char(100));
INSERT INTO bind_char VALUES(1, '1');
COMMIT;

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM bind_char WHERE C2 = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_CHAR(C, ':B1', '1');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.

DROP TABLE bind_char;
```

## BIND_ARRAY

```plsql
DBMS_SQL.BIND_ARRAY ( 
   c                   IN INTEGER, 
   name                IN VARCHAR2, 
   <table_variable>    IN <datatype> 
 [,index1              IN INTEGER, 
   index2              IN INTEGER)] ); 
```

BIND_ARRAY用于批量绑定变量，进行批量执行DML语句。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 绑定参数名称，VARCHAR类型。需要与语句内的绑定变量名称匹配。|
| table_variable | 绑定到绑定变量的值，类型需要是DBMS_SQL中定义的公共嵌套表类型。|
| index1 | 绑定的table成员的下限索引。|
| index2 | 绑定的table成员的上限索引。|

说明：

- index1和index2中间可以是不连续的。
- BIND_ARRAY和DEFINE_ARRAY不能同时使用。

示例

```plsql
DECLARE
  V_INT_TABLE DBMS_SQL.NUMBER_TABLE := DBMS_SQL.NUMBER_TABLE(1=>1, 2=>2);
  V_VARCHAR_TABLE DBMS_SQL.VARCHAR2_TABLE := DBMS_SQL.VARCHAR2_TABLE(1=>'Operation Dep', 2=>'QA Dep');
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'INSERT INTO department VALUES(:1, :2)';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_ARRAY(C, ':1', V_INT_TABLE);
  DBMS_SQL.BIND_ARRAY(C, ':2', V_VARCHAR_TABLE);
  R := DBMS_SQL.EXECUTE(C);
  COMMIT;
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.

SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep
1            Operation Dep
2            QA Dep

6 rows fetched.

DELETE FROM department WHERE deparment_no IN ('1','2');
```

## DEFINE_COLUMN

```plsql
DBMS_SQL.DEFINE_COLUMN (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN <datatype>);
```

或：

```plsql
DBMS_SQL.DEFINE_COLUMN (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN VARCHAR2,
   column_size    IN INTEGER);
```

DEFINE_COLUMN用于定义给定游标的投影列的位置。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 定义列的相对位置，INTEGER类型，下标从1开始。|
| column | 定义列的值，由值的类型确定所定义列的类型。|
| column_size | VARCHAR类型列的列值的期望大小。|

说明：

- column为CHAR或NCHAR类型时，必须指定column_size。
- 如果指定了column_size，则视作column为VARCHAR类型。

示例

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

PL/SQL Succeed.
```

## DEFINE_COLUMN_RAW

```plsql
DBMS_SQL.DEFINE_COLUMN_RAW (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN RAW,
   column_size    IN INTEGER);
```

DEFINE_COLUMN_RAW用于定义给定游标的投影列的位置，列的值为RAW类型。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 定义列的相对位置，INTEGER类型，下标从1开始。|
| column | 定义列的值，RAW类型。|
| column_size | RAW类型列的列值的期望大小。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10);
BEGIN
  V_SQL := 'SELECT ''6364'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_RAW(C, 1, V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_RAW(C, 1, V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6364

PL/SQL Succeed.
```

## DEFINE_COLUMN_CHAR

```plsql
DBMS_SQL.DEFINE_COLUMN_CHAR (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN CHAR,
   column_size    IN INTEGER);
```

DEFINE_COLUMN_CHAR用于定义给定游标的投影列的位置，列的值为CHAR类型。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 定义列的相对位置，INTEGER类型，下标从1开始。|
| column | 定义列的值，CHAR类型。|
| column_size | CHAR类型列的列值的期望大小。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10);
BEGIN
  V_SQL := 'SELECT ''abc'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_CHAR(C, 1, V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_CHAR(C, 1, V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
abc       

PL/SQL Succeed.
```

## DEFINE_ARRAY

```plsql
DBMS_SQL.DEFINE_ARRAY (
   c           IN INTEGER, 
   position    IN INTEGER,
   <table_variable>    IN <datatype> 
   cnt         IN INTEGER, 
   lower_bnd   IN INTEGER);
```

DEFINE_ARRAY用于定义需要FETCH的列，通过此过程从SELECT语句中FETCH多行结果集到嵌套表变量。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 定义列的相对位置，INTEGER类型，下标从1开始。|
| table_variable | 定义为DBMS_SQL内置嵌套表类型的变量，数据类型为DBMS_SQL中定义的嵌套表类型。|
| cnt | 需要获取的结果集行数。|
| lower_bnd | 结果集复制到嵌套表变量的下限索引位置。|

说明：

- 在执行FETCH_ROWS时将结果集发送到缓冲区，更新当前索引位置，执行COLUMN_VALUE时将缓冲区值赋值到变量，更新下限索引位置。
- DEFINE_ARRAY和BIND_ARRAY不能同时使用。

示例

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_NUMBER_TABLE DBMS_SQL.NUMBER_TABLE;
  V_VARCHAR_TABLE DBMS_SQL.VARCHAR2_TABLE;
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_ARRAY(C, 1, V_NUMBER_TABLE, 2, 1);
  DBMS_SQL.DEFINE_ARRAY(C, 2, V_VARCHAR_TABLE, 2, 1);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_NUMBER_TABLE);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR_TABLE);
  FOR I IN V_NUMBER_TABLE.FIRST .. V_NUMBER_TABLE.LAST LOOP
    DBMS_OUTPUT.PUT_LINE(i || ': ' || V_NUMBER_TABLE(i) || ', ' ||V_VARCHAR_TABLE(i));
  END LOOP;
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/
--result
1: 0, Public Dep
2: 10, Sales Dep

PL/SQL Succeed.
```

## EXECUTE

```plsql
DBMS_SQL.EXECUTE (
   c   IN INTEGER)
  RETURN INTEGER;
```

EXECUTE用于执行游标语句，返回已处理的行数，INTEGER类型。只有游标语句为INSERT、UPDATE和DELETE语句的返回值才有效。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

PL/SQL Succeed.
```

## FETCH_ROWS

```plsql
DBMS_SQL.FETCH_ROWS (
   c              IN INTEGER)
  RETURN INTEGER;
```

FETCH_ROWS用于获取结果集，返回实际获取的行数，INTEGER类型。对于DEFINE_ARRAY的游标，一次将获取多行结果集。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|

示例

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

PL/SQL Succeed.
```

## EXECUTE_AND_FETCH

```plsql
DBMS_SQL.EXECUTE_AND_FETCH (
   c              IN INTEGER,
   exact          IN BOOLEAN DEFAULT FALSE)
  RETURN INTEGER;
```

EXECUTE_AND_FETCH执行给定的游标并获取结果集。返回实际获取的行数，INTEGER类型。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| exact | 用于指定是否检查结果集行数。设置为TRUE时，检查返回的结果集是否为1行，否则报错。设置为FALSE则不检查返回的结果集行数。|

示例

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE_AND_FETCH(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

PL/SQL Succeed.
```

## VARIABLE_VALUE

```plsql
DBMS_SQL.VARIABLE_VALUE (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT NOCOPY <datatype>);
```

VARIABLE_VALUE用于获取绑定变量的值。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 获取绑定变量值的名称，需要与语句中的绑定变量名称匹配，VARCHAR类型。|
| value | 获取绑定变量值赋值的变量，可为SQL语句支持的任一类型。|

说明：

- name参数中的前导冒号可以省略。
- VARIABLE_VALUE中value的数据类型需要与绑定阶段绑定变量的数据类型相匹配。

示例

```plsql
INSERT INTO department VALUES('121', 'Service Dep');

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department WHERE department_name = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE(C, ':B1', 'Service Dep');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE(C, 'B1', V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
Service Dep

PL/SQL Succeed.

DELETE FROM department WHERE deparment_no='121';
```

## VARIABLE_VALUE_RAW

```plsql
DBMS_SQL.VARIABLE_VALUE_RAW (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT RAW);
```

VARIABLE_VALUE_RAW用于获取RAW类型绑定变量的值。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 获取绑定变量值的名称，需要与语句中的绑定变量名称匹配，VARCHAR类型。|
| value | 获取绑定变量值赋值的变量，RAW类型。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10) := '6364';
BEGIN
  V_SQL := 'begin :B1 := ''6566''; end;';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_RAW(C, ':B1', V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE_RAW(C, 'B1', V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6566

PL/SQL Succeed.
```

## VARIABLE_VALUE_CHAR

```plsql
DBMS_SQL.VARIABLE_VALUE_CHAR (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT CHAR);
```

VARIABLE_VALUE_CHAR用于获取CHAR类型绑定变量的值。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| name | 获取绑定变量值的名称，需要与语句中的绑定变量名称匹配，VARCHAR类型。|
| value | 获取绑定变量值赋值的变量，CHAR类型。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10) := 'abc';
BEGIN
  V_SQL := 'begin :B1 := ''defg''; end;';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_CHAR(C, ':B1', V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE_CHAR(C, 'B1', V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
defg      

PL/SQL Succeed.
```

## COLUMN_VALUE

```plsql
DBMS_SQL.COLUMN_VALUE (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT <datatype> 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE返回游标获取的结果集中指定列的值并赋值给value。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 获取投影列的相对位置，INTEGER类型，下标从1开始。|
| value | 获取结果集指定列的值并赋值给value，可为SQL语句支持的任一类型。|
| column_error | 语法兼容。 |
| actual_length | 返回列截断前的实际长度。|

示例

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

PL/SQL Succeed.
```

## COLUMN_VALUE_RAW

```plsql
DBMS_SQL.COLUMN_VALUE_RAW (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT RAW 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE_RAW返回游标获取的结果集中指定列的RAW类型值并赋值给value。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 获取投影列的相对位置，INTEGER类型，下标从1开始。|
| value | 获取结果集指定列的值并赋值给value，RAW类型。|
| column_error | 语法兼容。 |
| actual_length | 返回列截断前的实际长度。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10);
BEGIN
  V_SQL := 'SELECT ''6364'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_RAW(C, 1, V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_RAW(C, 1, V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6364

PL/SQL Succeed.
```

## COLUMN_VALUE_CHAR

```plsql
DBMS_SQL.COLUMN_VALUE_CHAR (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT CHAR 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE_CHAR返回游标获取的结果集中指定列的CHAR类型值并赋值给value。

仅当游标中为QUERY语句时，可使用该子过程。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| position | 获取投影列的相对位置，INTEGER类型，下标从1开始。|
| value | 获取结果集指定列的值并赋值给value，CHAR类型。|
| column_error | 语法兼容。 |
| actual_length | 返回列截断前的实际长度。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10);
BEGIN
  V_SQL := 'SELECT ''abc'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_CHAR(C, 1, V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_CHAR(C, 1, V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
abc       

PL/SQL Succeed.
```

## IS_OPEN

```plsql
DBMS_SQL.IS_OPEN (
   c              IN INTEGER)
  RETURN BOOLEAN;
```

IS_OPEN用于判断游标是否处于打开状态。返回值为BOOLEAN类型，如果为打开状态，返回TRUE，如果入参为NULL，返回FALSE。

|  参数| 描述|
| :---- |:-----------|
| c | 用于判断游标的ID，INTEGER类型。|

示例

```plsql
DECLARE
  C INTEGER;
  IS_OPEN BOOLEAN;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  IS_OPEN := DBMS_SQL.IS_OPEN(C);
  DBMS_OUTPUT.PUT_LINE(IS_OPEN);
  DBMS_SQL.CLOSE_CURSOR(C);
  IS_OPEN := DBMS_SQL.IS_OPEN(C);
  DBMS_OUTPUT.PUT_LINE(IS_OPEN);
END;
/

--result
true
false

PL/SQL Succeed.
```

## TO_REFCURSOR

```plsql
DBMS_SQL.TO_REFCURSOR(
   cursor_number IN OUT INTEGER)
  RETURN SYS_REFCURSOR;
```

TO_REFCURSOR将一个打开、解析并执行过的游标转换为SYS_REFCURSOR，并返回转换后得到的SYS_REFCURSOR。

|  参数| 描述|
| :---- |:-----------|
| cursor_number | 需要转换的游标ID，INTEGER类型。<br/>执行成功后可作为输出参数，并将被自动赋值为NULL。 |

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  C_REF SYS_REFCURSOR;
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  R := DBMS_SQL.EXECUTE(C);
  C_REF := DBMS_SQL.TO_REFCURSOR(C);
  FETCH C_REF INTO V_INT;
  DBMS_OUTPUT.PUT_LINE(V_INT);
END;
/

--result
1

PL/SQL Succeed.
```

## TO_CURSOR_NUMBER

```plsql
DBMS_SQL.TO_CURSOR_NUMBER(
   rc IN OUT SYS_REFCURSOR)
  RETURN INTEGER;
```

TO_CURSOR_NUMBER将一个打开的动态游标转换为由DBMS_SQL管理的游标，并返回转换后得到的游标ID。

转换后的DBMS_SQL游标处于已执行状态，不可重复执行。

|  参数| 描述|
| :---- |:-----------|
| rc | 需要转换的动态游标，SYS_REFCURSOR类型。转换后该动态游标rc被设置为无效游标。 |

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  C_REF SYS_REFCURSOR;
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  OPEN C_REF FOR V_SQL;
  C := DBMS_SQL.TO_CURSOR_NUMBER(C_REF);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_OUTPUT.PUT_LINE(V_INT);
END;
/

--result
1

PL/SQL Succeed.
```

## DESCRIBE_COLUMNS

```plsql
DBMS_SQL.DESCRIBE_COLUMNS ( 
   c              IN  INTEGER, 
   col_cnt        OUT INTEGER, 
   desc_t         OUT DESC_TAB);
```

DESCRIBE_COLUMNS描述由DBMS_SQL打开并解析的游标的投影列。

|  参数| 描述|
| :---- |:-----------|
| c | 使用游标的ID，INTEGER类型。|
| col_cnt | 返回投影列的列数。|
| desc_t | 返回每个投影列的描述信息。 |

示例

```plsql
DECLARE
V_STR VARCHAR(100);
C INT;
COL_CNT INT;
REC_TABLE DBMS_SQL.DESC_TAB;
REC DBMS_SQL.DESC_REC;
R NUMBER;
COL_NUM NUMBER;
BEGIN
V_STR := 'SELECT deparment_no, department_name FROM department';
C := DBMS_SQL.OPEN_CURSOR;
DBMS_SQL.PARSE(C, V_STR, 1);
DBMS_SQL.DESCRIBE_COLUMNS(C, COL_CNT, REC_TABLE);
COL_NUM := REC_TABLE.FIRST;
WHILE COL_NUM IS NOT NULL LOOP
	REC := REC_TABLE(COL_NUM);
	DBMS_OUTPUT.PUT_LINE('----COL: ' || COL_NUM || '----');
    DBMS_OUTPUT.PUT_LINE('COL_TYPE            =    ' || REC.COL_TYPE);
    DBMS_OUTPUT.PUT_LINE('COL_MAXLEN          =    ' || REC.COL_MAX_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_NAME            =    ' || REC.COL_NAME);
    DBMS_OUTPUT.PUT_LINE('COL_NAME_LEN        =    ' || REC.COL_NAME_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_SCHEMA_NAME     =    ' || REC.COL_SCHEMA_NAME);
    DBMS_OUTPUT.PUT_LINE('COL_SCHEMA_NAME_LEN =    ' || REC.COL_SCHEMA_NAME_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_PRECISION       =    ' || REC.COL_PRECISION);
    DBMS_OUTPUT.PUT_LINE('COL_SCALE           =    ' || REC.COL_SCALE);
	  DBMS_OUTPUT.PUT_LINE('COL_CHARSETID       =    ' || REC.COL_CHARSETID);
	  DBMS_OUTPUT.PUT_LINE('COL_CHARSETFORM     =    ' || REC.COL_CHARSETFORM);
	  DBMS_OUTPUT.PUT_LINE('COL_NULL_OK         =    ' || REC.COL_NULL_OK);
	COL_NUM := REC_TABLE.NEXT(COL_NUM);
END LOOP;
DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
----COL: 1----
COL_TYPE            =    24
COL_MAXLEN          =    3
COL_NAME            =    DEPARMENT_NO
COL_NAME_LEN        =    12
COL_SCHEMA_NAME     =
COL_SCHEMA_NAME_LEN =    0
COL_PRECISION       =    0
COL_SCALE           =    0
COL_CHARSETID       =    2
COL_CHARSETFORM     =
COL_NULL_OK         =    false
----COL: 2----
COL_TYPE            =    26
COL_MAXLEN          =    20
COL_NAME            =    DEPARTMENT_NAME
COL_NAME_LEN        =    15
COL_SCHEMA_NAME     =
COL_SCHEMA_NAME_LEN =    0
COL_PRECISION       =    0
COL_SCALE           =    0
COL_CHARSETID       =    2
COL_CHARSETFORM     =
COL_NULL_OK         =    true

PL/SQL Succeed.
```

## LAST_ERROR_POSITION

```plsql
DBMS_SQL.LAST_ERROR_POSITION 
   RETURN INTEGER;
```

返回SQL语法的报错的字节偏移位置，偏移位置从0开始。

仅在DBMS_SQL.PARSE或DBMS_SQL.EXECUTE阶段报错且原有报错信息包含position信息时，才能获取到LAST_ERROR_POSITION。

示例

```plsql
DECLARE
C1 INT;
V_SQL VARCHAR(100);
BEGIN
C1 := DBMS_SQL.OPEN_CURSOR();
V_SQL := 'SELECT X FROM DUAL';
DBMS_SQL.PARSE(C1, V_SQL, DBMS_SQL.NATIVE);
EXCEPTION WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(DBMS_SQL.LAST_ERROR_POSITION || ', ' || SQLERRM);
END;
/

--result
7, YAS-04243 invalid identifier "X"
```

## RETURN_RESULT

```plsql
DBMS_SQL.RETURN_RESULT (
rc              IN OUT SYS_REFCURSOR,
to_client       IN BOOLEAN DEFAULT TRUE);
```

RETURN_RESULT为存储过程，用于执行指定SQL并返回结果集给客户端（例如接口程序），客户端获得结果集游标后，可以执行fetch语句查询结果。

当在yasql中执行本存储过程时，将直接输出结果集内容。

|  参数| 描述|
| --------- | ------------------------------------------------------------ |
| rc        | 动态游标。                                                     |
| to_client | 是否返回结果集给客户端；默认为true，如果传入false并且是执行动态SQL时，结果集不返回给客户端。 |

> **Note**: 
>
> - 当前只有查询结果集可以返回。
> - 执行返回结果集后，rc参数对应的游标不能再访问。
> - 客户端将缓存结果集的全部结果，但依赖客户端所在设备的内存大小。
> - 过程体执行过程中如果发生错误，客户端依然可以获取到错误点之前返回的结果集。

示例

```sql
CREATE OR REPLACE PROCEDURE return_rs_proc IS
cur1 sys_refcursor;
cur2 sys_refcursor;
BEGIN
  OPEN cur1 for select area_no,area_name from area where area_no='01';
  DBMS_SQL.RETURN_RESULT(cur1,false);
  OPEN cur2 for select branch_no,branch_name from branches where area_no='01';
  DBMS_SQL.RETURN_RESULT(cur2);
END;
/

-- 执行静态SQL
exec return_rs_proc;
PL/SQL Succeed.

ResultSet #1

AREA_NO AREA_NAME                                                     
------- ------------------------------------------------------------- 
01      EastChina                                                       

1 row fetched.

ResultSet #2

BRANCH_NO BRANCH_NAME                                                      
--------- ---------------------------------------------------------------- 
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          

4 rows fetched.

-- 执行动态SQL，此时由于to_client为false，第一个结果集不会返回
BEGIN
  execute immediate 'begin return_rs_proc; end;';
END;
/
PL/SQL Succeed.

ResultSet #1

BRANCH_NO BRANCH_NAME                                                      
--------- ---------------------------------------------------------------- 
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          

4 rows fetched.
```

## 常量

|  常量| 数据类型| 值| 说明|
| :---- | :---- | :---- | :---- |
| NATIVE| INTEGER | 1 | 指定程序连接的正常行为 |

## 异常

|  异常| 描述|
| :---- | :---- |
|INCONSISTENT_TYPE|当COLUMN_VALUE子过程或VARIABLE_VALUE子过程给定的出参类型与先前定义的类型不匹配时，抛出此异常。|

示例

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_VARCHAR);
  EXCEPTION WHEN DBMS_SQL.INCONSISTENT_TYPE THEN
    DBMS_OUTPUT.PUT_LINE('type of out argument must match type of column or bind variable');
    DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result 
type of out argument must match type of column or bind variable

PL/SQL Succeed.
```

## DBMS_SQL DESC_REC Record Type

作为DESC_TAB嵌套表类型的成员类型，描述投影列信息。

```plsql
TYPE desc_rec IS RECORD (
      col_type            BINARY_INTEGER := 0,
      col_max_len         BINARY_INTEGER := 0,
      col_name            VARCHAR2(64)   := '',
      col_name_len        BINARY_INTEGER := 0,
      col_schema_name     VARCHAR2(64)   := '',
      col_schema_name_len BINARY_INTEGER := 0,
      col_precision       BINARY_INTEGER := 0,
      col_scale           BINARY_INTEGER := 0,
      col_charsetid       BINARY_INTEGER := 0,
      col_charsetform     BINARY_INTEGER := 0,
      col_null_ok         BOOLEAN        := TRUE);
TYPE desc_tab IS TABLE OF desc_rec INDEX BY BINARY_INTEGER;
```

|  字段| 类型| 默认值| 说明|
| :---- | :---- | :---- | :---- |
|col_type	| BINARY_INTEGER|0|列类型。|
|col_max_len | BINARY_INTEGER|0|最大列长度。|
|col_name	| VARCHAR2(64) | NULL| 列名称。|
|col_name_len	| BINARY_INTEGER |	0	|列名称长度。|
|col_schema_name | VARCHAR2(64)|NULL|列schema名称。|
|col_schema_name_len | BINARY_INTEGER	|0|列schema名称长度。|
|col_precision | BINARY_INTEGER	| 0	| 列precision。|
|col_scale | BINARY_INTEGER	| 0|列scale。|
|col_charsetid | BINARY_INTEGER	|0	|列字符串id。|
|col_charsetform | BINARY_INTEGER	|0	|列字符集格式（保留字段）。|
|col_null_ok | BOOLEAN | TRUE | 列是否可能为NULL，TRUE说明可能为NULL。|

## DBMS_SQL DESC_TAB Table Type

用于保存describe_column获取列描述信息。

```plsql
TYPE desc_tab IS TABLE OF desc_rec INDEX BY BINARY_INTEGER;
```

## DBMS_SQL TABLE Types For Scalar and LOB Collections

标量和LOB类型的嵌套表定义。

```plsql
TYPE binary_double_table IS TABLE OF BINARY_DOUBLE  INDEX BY BINARY_INTEGER;
TYPE binary_float_table IS TABLE OF BINARY_FLOAT   INDEX BY BINARY_INTEGER;
TYPE blob_table     IS TABLE OF BLOB           INDEX BY BINARY_INTEGER;
TYPE clob_table     IS TABLE OF CLOB           INDEX BY BINARY_INTEGER;
TYPE date_table     IS TABLE OF DATE           INDEX BY BINARY_INTEGER;
TYPE interval_day_to_second_table IS TABLE OF INTERVAL DAY TO SECOND INDEX BY BINARY_INTEGER;
TYPE interval_year_to_month_table IS TABLE OF INTERVAL YEAR TO MONTH INDEX BY BINARY_INTEGER;
TYPE number_table   IS TABLE OF NUMBER         INDEX BY BINARY_INTEGER;
TYPE timestamp_table IS TABLE OF timestamp INDEX BY BINARY_INTEGER;
TYPE time_table IS TABLE OF TIME INDEX BY BINARY_INTEGER;
TYPE urowid_table IS TABLE OF UROWID INDEX BY BINARY_INTEGER;
TYPE varchar2_table IS TABLE OF VARCHAR2(65534) INDEX BY BINARY_INTEGER;
```
