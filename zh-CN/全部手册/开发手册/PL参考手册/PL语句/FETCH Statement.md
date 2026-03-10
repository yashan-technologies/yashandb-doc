FETCH Statement为[游标](../PL语言基础/变量/游标)取值语句，系统对显式游标和动态游标的取值方法一致，且格式均为：

_FETCH cursor_name \[BULK COLLECT\] INTO result_variable[,result_variable] limit numeric_expression;_

其中，cursor_name为显式游标或动态游标的名称，cursor_name不能是绑定参数。result_variable为一个已声明的变量，用于接收结果集；numeric_expression是数值文本、数值变量或数值表达式，用于限制FETCH获取的行数。

BULK COLLECT为可选项，当使用该项时，会将FETCH的检索结果一次性存储进一个或多个集合变量中。

当需要取值结果集的指定列项时，result_variable可以被声明为一个普通数据类型的变量（或其集合），可同时指定多个列项取值。

当需要取值整个结果集时，result_variable必须被声明为相同列结构的RECORD类型变量（或其集合）或等同于结果集列的多个数据类型变量（或其集合）。

上述两种情况中所定义的列项数据类型均须与结果集列项的数据类型兼容，兼容的含义为当数据类型不一致时系统可以成功执行隐式转换。

游标取值表示将游标指向查询结果集中的行记录，并将其输出到result_variable变量空间中。

## fetch..into

每执行一次FETCH Statement，游标指针移动一次。

游标指针只能往前移动，不能回退。

当前游标指针只能指向某一行，即每移动一次可获取到一行记录。

当游标指针已经走到结束，继续执行FETCH不会导致错误，但游标也不会取到值。

使用RECORD变量承载结果集：

示例

```plsql
DECLARE
  TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
  cur cursor;
  TYPE record IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 VARCHAR(20)
  );
  rec record;
BEGIN 
  OPEN cur FOR SELECT * FROM area;
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  CLOSE cur;
END;
/

--result
01 EastChina Shanghai
02 WestChina Chengdu
```

使用多个变量承载结果集：

示例
```plsql
DECLARE
  TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
  cur cursor;
  c1 CHAR(2);
  c2 VARCHAR(20);
  c3 VARCHAR(20);
BEGIN 
  OPEN cur FOR SELECT * FROM area;
  FETCH cur INTO c1, c2, c3;
  DBMS_OUTPUT.PUT_LINE(c1 ||' '||c2||' '||c3);
  FETCH cur INTO c1, c2, c3;
  DBMS_OUTPUT.PUT_LINE(c1 ||' '||c2||' '||c3);
  CLOSE cur;
END;
/

--result
01 EastChina Shanghai
02 WestChina Chengdu
```

针对多行结果集，更简便的办法是利用系统提供的游标属性，结合循环语句来进行游标取值。

下表为YashanDB提供的四种游标属性：

|  属性| 返回值类型| 作用|
| :-------- | :--------- | :----------------------- |
| %isopen   | 布尔型     | 判断游标是否打开         |
| %found    | 布尔型     | 判断游标是否获取到值     |
| %notfound | 布尔型     | 判断游标是否没有获取到值 |
| %rowcount | BIGINT     | 当前成功执行的数据行数   |

示例

```plsql
DECLARE
  CURSOR cur IS SELECT * FROM area;
  c1 CHAR(2);
  c2 VARCHAR(20);
  c3 VARCHAR(20);
BEGIN 
  OPEN cur;
  LOOP
    FETCH cur INTO c1,c2,c3;
    EXIT WHEN cur%notfound;
    DBMS_OUTPUT.PUT_LINE('Line ' || cur%rowcount || ':' );
    DBMS_OUTPUT.PUT_LINE(c1 ||' '||c2||' '||c3);
  END LOOP;  
  CLOSE cur;
END;
/

--result
Line 1:
01 EastChina Shanghai
Line 2:
02 WestChina Chengdu
Line 3:
03 SouthChina Guangzhou
Line 4:
04 NorthChina Beijing
Line 5:
05 CentralChina Wuhan
```

## fetch..bulk collect into

只需执行一次FETCH Statement即可将整个结果集提取到一个或多个集合变量中。

limit用于限制提取的行数。

示例

```plsql

DECLARE
  TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
  cur cursor;
  TYPE areaType IS TABLE OF area%rowtype;
  areaVar areaType;
BEGIN 
  OPEN cur FOR SELECT * FROM area;
  FETCH cur BULK COLLECT INTO areaVar;
  FOR i IN areaVar.first..areaVar.last LOOP
    DBMS_OUTPUT.PUT_LINE(areaVar(i).area_no  ||' '||areaVar(i).area_name ||' '||areaVar(i).DHQ );
  END LOOP;
  CLOSE cur;
END;
/

--result
01 EastChina Shanghai
02 WestChina Chengdu
03 SouthChina Guangzhou
04 NorthChina Beijing
05 CentralChina Wuhan
```
