DML Statement为静态SQL执行语句，其语法形式与常规DML类的SQL语句一致，且可以结合INTO语法为PL的变量或绑定参数赋值。

在过程体中，DML语句可直接执行或通过[EXECUTE Statement](./EXECUTE Statement)调用执行，此时同样可以结合INTO语法使用。

## 常规用法

不与INTO结合时，静态SQL不能为SELECT语句。

示例

```Plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
BEGIN
UPDATE area SET area_no='00' WHERE area_no='03';  
DELETE FROM area WHERE area_no='03'; 
INSERT INTO area 
SELECT area_no+20, area_name, dhq 
FROM area 
WHERE area_no IN ('01','02','03');
COMMIT;      
END;
/

exec ya_proc;
```

## select...into

本语句可以将SELECT语句从数据库中查询到结果赋值给变量，但查询的结果只能是一行记录，不能是零行或多行记录。

对于不能确保获得一行结果集的SELECT语句，需通过[游标](../../全部手册/开发手册/PL参考手册/PL语言基础/变量/游标)实现结果集赋值。

执行SELECT INTO语句时，若发生TOO MANY ROWS异常，在抛出异常前已经将第一行记录赋值给变量。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
BEGIN
SELECT area_no,area_name INTO no,name FROM area WHERE area_no='01';
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
 
--下面语句将抛出NO_DATA_FOUND异常，通过EXCEPTION Statement将其捕获
SELECT area_no,area_name INTO no,name FROM area WHERE area_no='09';   
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
EXCEPTION
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('warning:no data found!');
END;
/
  
exec ya_proc;
  
--result
AREANO:AREANAME:EastChina
warning:no data found!
```

## select...bulk collect into

本语句可以将SELECT语句从数据库中查询的结果赋值给集合变量，查询的结果可以是零行或任意数量行。

对使用字符串类型作键的关联数组，不允许使用BULK COLLECT子句。

BULK COLLECT INTO的目标对象必须是集合类型。

示例

```plsql

DECLARE
  TYPE area_tb IS TABLE OF area%rowtype;
  type_001 area_tb;
BEGIN
  SELECT * BULK COLLECT INTO type_001 FROM area ORDER BY 1,2;
  FOR i IN type_001.first..type_001.last LOOP
    DBMS_OUTPUT.PUT_LINE('type_001('||i||'): ' || type_001(i).area_no  || CHR(9) || type_001(i).area_name );
  END LOOP;
END;
/

--result
type_001(1): 01 EastChina
type_001(2): 02 WestChina
type_001(3): 03 SouthChina
type_001(4): 04 NorthChina
type_001(5): 05 CentralChina
```

## insert into...return...into

本语句在执行INSERT操作的同时，指定一个返回的结果集，并将其赋值给变量。其中RETURN也可以写为RETURNING。

结果集可以包含一列或多列数据，每一列为一个[YashanDB通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，但不能为序列、伪列、聚集函数、窗口函数、子语句。

在此种用法中，INSERT语句只能为[insert_values_clause](../../全部手册/开发手册/SQL参考手册/SQL语句（yashan模式）/INSERT)语法，不可以为多行或者子查询插入。

在此种用法中，[returning_clause](../../全部手册/开发手册/SQL参考手册/SQL语句（yashan模式）/INSERT)语法，不可返回变量。

列存表无法使用insert into...return...into语句。

示例（HEAP表）

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
  no INT;
  v_area_name VARCHAR(20);
BEGIN
  INSERT INTO area(area_no) VALUES('09') 
  RETURN TO_NUMBER(area_no), NVL(area_name, 'area'||area_no) INTO no, v_area_name;
  DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||v_area_name);
END;
/

exec ya_proc;
  
--result
AREANO:AREANAME:area09
```

## update...return...into

本语句在执行UPDATE操作的同时，指定一个返回的结果集，并将其赋值给变量。其中RETURN也可以写为RETURNING。

结果集可以包含一列或多列数据，每一列为一个[YashanDB通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，但不能为序列、伪列、聚集函数、窗口函数、变量、子语句。

在此种用法中，[returning_clause](../../全部手册/开发手册/SQL参考手册/SQL语句（yashan模式）/UPDATE.html#returningclause)语法，不可返回变量。

列存表无法使用update...return...into语句；涉及多表更新时无法使用update...return...into语句。

示例（HEAP表）

```plsql

CREATE OR REPLACE PROCEDURE ya_proc IS
  no INT;
  v_area_name VARCHAR(20);
BEGIN
  UPDATE area SET area_no = '09' WHERE area_no = '09'
  RETURN TO_NUMBER(area_no), NVL(area_name, 'area'||area_no) INTO no, v_area_name;
  DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||v_area_name);
END;
/

exec ya_proc;

*--result*
AREANO:AREANAME:area09
```

## update...return...bulk collect into

本语句将先执行UPDATE语句，执行后从数据库中查询UPDATE后的结果赋值给集合变量，语句的结果可以是零行或任意数量行。

BULK COLLECT INTO的目标对象必须是集合类型。

示例（HEAP表）

```plsql

DECLARE
  TYPE area_tb IS TABLE OF area%rowtype;
  type_001 area_tb;
BEGIN
  UPDATE area SET area_no = area_no RETURNING area_no, area_name, dhq BULK COLLECT INTO type_001;
  FOR i IN type_001.first..type_001.last LOOP
​    DBMS_OUTPUT.PUT_LINE('type_001('||i||'): ' || type_001(i).area_no  || CHR(9) || type_001(i).area_name );
  END LOOP;
END;
/

*--result*
type_001(1): 01 EastChina
type_001(2): 02 WestChina
type_001(3): 03 SouthChina
type_001(4): 04 NorthChina
type_001(5): 05 CentralChina
```
