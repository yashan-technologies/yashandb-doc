The FETCH Statement is a value retrieval statement for [cursors](../PL Language Fundamentals/Variables/Cursors). The method for retrieving values for explicit cursors and dynamic cursors is consistent, and the format is as follows:

_FETCH cursor_name \[BULK COLLECT\] INTO result_variable[,result_variable] limit numeric_expression;_

Here, cursor_name is the name of the explicit or dynamic cursor, and cursor_name cannot be a bind parameter. result_variable is a declared variable used to receive the result set; numeric_expression is a numeric text, numeric variable, or numeric expression used to limit the number of rows fetched by FETCH.

BULK COLLECT is optional. When this option is used, the results retrieved by FETCH will be stored all at once into one or more collection variables.

When specific column items in the result set need to be retrieved, result_variable can be declared as a variable of a standard data type (or its collection), allowing for multiple column item retrievals simultaneously.

When the entire result set needs to be retrieved, result_variable must be declared as a RECORD type variable (or its collection) with the same column structure or multiple data type variables (or their collections) equivalent to the result set columns.

The data types defined for the column items in the above two cases must be compatible with the data types of the result set columns. Compatibility means that the system can successfully perform implicit conversion when the data types are inconsistent.

The cursor retrieval indicates moving the cursor to a row record in the query result set and outputting it to the result_variable variable space.

## fetch..into

Each time a FETCH Statement is executed, the cursor pointer moves once.

The cursor pointer can only move forward, it cannot go back.

The current cursor pointer can only point to a single row, meaning that each move retrieves one row record.

When the cursor pointer reaches the end, continuing to execute FETCH will not cause an error, but the cursor will not retrieve values.

Using a RECORD variable to hold the result set:

***Example***

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

Using multiple variables to hold the result set:

***Example***
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

For multi-row result sets, a more convenient method is to use the cursor attributes provided by the system, along with loop statements for cursor value retrieval.

The following table lists the four cursor attributes provided by YashanDB:

|Attribute |Return Type |Function |
| :-------- | :--------- | :----------------------- |
| %isopen    | Boolean     | Check if the cursor is open  |
| %found     | Boolean     | Check if the cursor has fetched values |
| %notfound  | Boolean     | Check if the cursor has not fetched values |
| %rowcount  | BIGINT      | Number of rows successfully processed |

***Example***

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

Only one execution of the FETCH Statement is needed to extract the entire result set into one or more collection variables.

limit is used to restrict the number of rows extracted.

***Example***

```plsql

DECLARE
  TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
  cur cursor;
  TYPE areaType IS table of area%rowtype;
  areaVar areaType;
BEGIN 
  OPEN cur FOR SELECT * FROM area;
  FETCH cur BULK COLLECT INTO areaVar;
  for i in areaVar.first..areaVar.last loop
    DBMS_OUTPUT.PUT_LINE(areaVar(i).area_no  ||' '||areaVar(i).area_name ||' '||areaVar(i).DHQ );
  end loop;
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
