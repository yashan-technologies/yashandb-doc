FORALL Statement为批量执行语句。

语句定义
-------

**forall::=**

```ebnf+diagram
normal_clause::= lower_bound..upper_bound
indices_of_clause::= INDICES OF collection [ BETWEEN lower_bound AND upper_bound ]
values_of_clause::= VALUES OF collection
forall::= FORALL index IN (normal_clause | indices_of_clause | values_of_clause) [SAVE EXCEPTIONS] dml_statement
```

IN关键字后有三种可选子句，选择普通子句时，可以直接以两个值作为index的上下限。lower_bound为索引下限值，upper_bound为索引上限值。index、lower_bound、upper_bound均为INTEGER类型，取值范围同INTEGER值域。

INDICES_OF子句用集合类型（VARRAY与NESTED-TABLE）变量的索引作为循环中的索引值，lower_bound、upper_bound同样作为索引上下限。

VALUES_OF子句用集合类型（VARRAY与NESTED-TABLE）变量的成员值作为循环中的索引值。

SAVE EXCEPTIONS为可选项，它能使在某些dml statement执行失败的情况下继续执行FORALL语句，并将错误信息保存至FORALL特有的游标内，当整个FORALL语句执行完后，抛出一个统一错误码（YAS-06817）。

FORALL Statement的执行体必须是一个单独的dml statement，例如INSERT，UPDATE或DELETE。

**index::=**

index为FORALL Statement里隐式声明的局部变量，只能在FORALL语句内部使用，退出FORALL语句后失效。

**lower\_bound|upper\_bound::=**

lower_bound和upper_bound为数值文本、数值变量或数值表达式。
在普通子句中时，按步进1递增，在INDICES_OF子句中时，将直接跳进到下一个存在数组成员的索引值：

*   当lower_bound < upper_bound时，从lower_bound开始循环赋值给index，一直到upper_bound即结束循环语句。
*   当lower_bound = upper_bound时，lower_bound赋值给index，循环语句只会执行一次。
*   当lower_bound > upper_bound时，直接结束FORALL语句。

**collection::=**

集合类型（VARRAY或NESTED-TABLE）变量或绑定参数，用于作为INDICES_OF与VALUES_OF的访问对象。

- 当作为INDICES OF的访问对象时，该数组不允许使用通过字符串索引的数组。
- 当作为VALUES OF的访问对象时，仅能使用成员类型为int型的数组。

游标定义
-------

**SQL%BULK_EXCEPTIONS**

在YAS-06817的异常处理流程中，可以从隐式游标属性SQL%BULK_EXCEPTIONS获取有关每个DML语句失败的信息。
- SQL%BULK_EXCEPTIONS.COUNT：记录失败的DML语句的条数。
- SQL%BULK_EXCEPTIONS(i).ERROR_INDEX：记录第i个失败的DML语句在所有DML语句中的位置。
- SQL%BULK_EXCEPTIONS(i).ERROR_CODE：记录第i个失败的DML语句的错误码。

**SQL%BULK_ROWCOUNT**

FORALL执行完毕后，可以从隐式游标属性SQL%BULK_ROWCOUNT获取每条DML影响的行数。

SQL%BULK_ROWCOUNT(i)：记录第i条DML语句执行完毕后所影响的行数。

示例

```plsql

DECLARE 
    TYPE numListType IS TABLE OF CHAR(2);
	numList numListType := numListType('01','02','03');
BEGIN
    forall i IN 1..6/1.9 save exceptions
        UPDATE area SET DHQ  = DHQ  || '3out_of_size' WHERE area_no  = numList(i);
    EXCEPTION
        WHEN others THEN 
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS.COUNT : ' || SQL%BULK_EXCEPTIONS.COUNT );

            FOR i IN 1..SQL%BULK_EXCEPTIONS.COUNT LOOP
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS('||i||').ERROR_CODE : ' || SQL%BULK_EXCEPTIONS(i).ERROR_CODE );
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS('||i||').ERROR_INDEX :' || SQL%BULK_EXCEPTIONS(i).ERROR_INDEX );
            END LOOP;
            
            FOR i IN 1..3 LOOP   ----4 out
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_ROWCOUNT('||i||') : ' || SQL%BULK_ROWCOUNT(i) );
            END LOOP; 
END;
/

--result
SQL%BULK_EXCEPTIONS.COUNT : 1
SQL%BULK_EXCEPTIONS(1).ERROR_CODE : 4008
SQL%BULK_EXCEPTIONS(1).ERROR_INDEX :3
SQL%BULK_ROWCOUNT(1) : 1
SQL%BULK_ROWCOUNT(2) : 1
SQL%BULK_ROWCOUNT(3) : 0

-- INDICES OF
DECLARE 
    TYPE numListType IS TABLE OF INT INDEX BY INT;
	numList numListType := numListType();
BEGIN
    numList(-5) := -5;
    numList(-1.5) := -1.5;
    numList(25) := 25;
    numList(10) := 10;
    forall i IN indices OF numList BETWEEN -3 AND 15
        INSERT INTO area VALUES(numList(i), NULL, 'area' || numList(i));
END;
/

SELECT area_no,area_name,DHQ FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
-2                                                                    area-2
10                                                                    area10

-- VALUES OF
DECLARE 
    TYPE charListType IS TABLE OF CHAR(2);
	charList charListType := charListType('A', 'B', 'C', 'D');
	
	TYPE numListType IS VARRAY(10) OF INT;
	indexList numListType := numListType(1, 3, 4);
BEGIN
    forall i IN VALUES OF indexList
        INSERT INTO area VALUES(charList(i), NULL, 'area' || charList(i));
END;
/

SELECT area_no,area_name,DHQ FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
A                                                                     areaA
C                                                                     areaC
D                                                                     areaD

```
