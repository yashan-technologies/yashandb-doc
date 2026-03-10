OPEN Statement为打开[游标](../PL语言基础/变量/游标)的语句，打开游标表示执行游标所绑定（静态绑定或动态绑定）的SQL语句，并获得查询结果集。

打开显式游标
------------

打开一个显式游标的语法格式为：

_OPEN cursor[cursor_parameters];_

其中cursor为显式游标名称，cursor不能是绑定参数。cursor_parameters为显式游标参数的输入值。

如显式游标在声明阶段为某个参数定义了缺省值，则此处其对应的cursor_parameters可省略，否则每个声明的参数在本语句里必须有对应的输入值。

声明游标阶段的cursor_parameters与打开游标阶段的cursor_parameters按顺序一一对应，除非通过"=>"指定了参数的传递顺序。

显式游标在被打开后将保留对应的查询结果集，在将其关闭之前不允许再次执行打开操作。

示例

```plsql
DECLARE 
  CURSOR cur(areano CHAR DEFAULT '02', branchno CHAR, orderdate DATE DEFAULT SYSDATE) IS
  SELECT area, branch, order_no FROM orders_info 
  WHERE area = areano 
  AND branch = branchno 
  AND order_date < orderdate;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 CHAR(4),
	c3 VARCHAR(20)
  );
  rec record1;
BEGIN 
  DBMS_OUTPUT.PUT_LINE('First open:');
  OPEN cur(branchno =>'0201');
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' ,'||rec.c2 ||' ,'||rec.c3);
  CLOSE cur;
  DBMS_OUTPUT.PUT_LINE('Second open:');
  OPEN cur('02','0201');
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' ,'||rec.c2 ||' ,'||rec.c3);
  CLOSE cur;
END;
/

--result
First open:
02 ,0201 ,20010102020001
Second open:
02 ,0201 ,20010102020001
```

打开动态游标
-------

打开一个动态游标的语法格式为：

_OPEN cursor_variable FOR select_statement;_

其中，cursor_variable为游标变量名称；select_statement为该游标要绑定的SQL语句，该SQL语句可以是静态SQL语句，也可以是通过变量承载的动态SQL语句。当使用静态SQL语句时可以使用INTO子句，此时只做语法兼容，没有实际作用。当使用动态SQL语句时可以使用USING子句加入[绑定参数](../参数/绑定参数)。此时语法格式变为：

_OPEN cursor_variable FOR string_variable USING bind_variable_list;_

一个动态游标可以被多次打开（可以绑定不同的SQL语句，且返回值类型也可以不同），且不用显式指定关闭游标语句。但在最终不再使用该游标时，应该显式地执行关闭游标操作。

静态SQL语句：

示例

```plsql
DECLARE
  TYPE cursor IS REF CURSOR;
  cur cursor;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20)
  );
  rec1 record1;
  TYPE record2 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 INT
  );
  rec2 record2;
BEGIN 
  OPEN cur FOR SELECT area_no, area_name FROM area WHERE area_no='01';
  FETCH cur INTO rec1;
  DBMS_OUTPUT.PUT_LINE(rec1.c1 ||' '||rec1.c2);
  OPEN cur FOR SELECT area_no, area_name, LENGTH(area_name) FROM area WHERE area_no='02';
  FETCH cur INTO rec2;
  DBMS_OUTPUT.PUT_LINE(rec2.c1 ||' '||rec2.c2||' '||rec2.c3);
  CLOSE cur;
END;
/

--result
01 EastChina
02 WestChina 2
```

动态SQL语句：

示例
```plsql
DECLARE
  TYPE cursor IS REF CURSOR;
  cur cursor;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20)
  );
  rec1 record1;
  TYPE record2 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 INT
  );
  rec2 record2;
  sql1 CHAR(200) := 'SELECT area_no, area_name FROM area WHERE area_no = :1';
  sql2 CHAR(200) := 'SELECT area_no, area_name, LENGTH(area_name) FROM area WHERE area_no= :1';
  bind1 CHAR(5) := '01';
  bind2 CHAR(5) := '02';
BEGIN 
  OPEN cur FOR sql1 USING bind1;
  FETCH cur INTO rec1;
  DBMS_OUTPUT.PUT_LINE(rec1.c1 ||' '||rec1.c2);
  OPEN cur FOR sql2 USING bind2;
  FETCH cur INTO rec2;
  DBMS_OUTPUT.PUT_LINE(rec2.c1 ||' '||rec2.c2||' '||rec2.c3);
  CLOSE cur;
END;
/

--result
01 EastChina
02 WestChina 2
```

通过IS [NOT] NULL运算符可以判断动态游标是否绑定过SQL语句：

_cursor_variable IS [NOT] NULL_

其中，cursor_variable为游标变量名称。

- IS NULL：

  - 动态游标未绑定过SQL语句，结果为TRUE。

  - 动态游标已绑定SQL语句，结果为FALSE。


- IS NOT NULL：

  - 动态游标已绑定SQL语句，结果为TRUE。

  - 动态游标未绑定过SQL语句，结果为FALSE。

```plsql
DECLARE
c SYS_REFCURSOR;
BEGIN
IF c IS null THEN
    dbms_output.put_line('BEFORE OPEN NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('BEFORE OPEN NOT NULL');
END IF;

-- OPEN
OPEN c FOR SELECT * FROM dual;

IF c IS null THEN
    dbms_output.put_line('AFTER OPEN NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('AFTER OPEN NOT NULL');
END IF;

-- CLOSE
CLOSE c;

IF c IS null THEN
    dbms_output.put_line('AFTER CLOSE NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('AFTER CLOSE NOT NULL');
END IF;

dbms_output.put_line('END');
END;
/

--result
BEFORE OPEN NULL
AFTER OPEN NOT NULL
AFTER CLOSE NOT NULL
END
```