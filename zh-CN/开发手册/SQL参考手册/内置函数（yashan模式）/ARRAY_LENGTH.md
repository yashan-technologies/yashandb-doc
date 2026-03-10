```ebnf+diagram
array_length::= ARRAY_LENGTH "(" array_var "," dimension ")"
```

ARRAY_LENGTH函数对数组（[数组变量](../../PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../PL参考手册/PL对象/自定义类型)，[数组类型](../数据类型（yashan模式）/用户自定义类型)）array_var计算按dimension指定维度的数组长度（即成员数量），返回一个INT类型的数值。

本函数不支持向量化计算。

**array_var**

一个已初始化的数组变量，不可为NULL。

**dimension**

数组维度，即数组嵌套深度，为一个[通用表达式](../通用SQL语法/expr)，其值类型必须为INT（超过INT值域范围时报错）。

- 当dimension为NULL时，函数返回NULL。
- 数组嵌套深度从1开始，当dimension小于1或者大于最大深度时，函数返回NULL。
- 当在同一dimension存在多个数组时，函数返回最长的数组长度。

示例（HEAP表）

```sql
--单层数组
SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), 1) len FROM DUAL;
         LEN 
------------ 
           5

SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), NULL) len FROM DUAL;
         LEN 
------------ 

SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), -1) len FROM DUAL;
         LEN 
------------ 

--多层数组
SET serveroutput ON
DECLARE
  TYPE arr_type_2 IS VARRAY(10) OF CHAR(5);
  TYPE arr_type_1 IS VARRAY(10) OF arr_type_2;
  b arr_type_1;
BEGIN
    b := arr_type_1(STRING_TO_ARRAY('a1,b1,c1', ',')
                    ,STRING_TO_ARRAY('a2,b2', ',')
                    ,STRING_TO_ARRAY('a3,b3,c3,d3', ','));
    DBMS_OUTPUT.PUT_LINE('Dimension 1 has:'||ARRAY_LENGTH(b,1));
    DBMS_OUTPUT.PUT_LINE('Dimension 2 has:'||ARRAY_LENGTH(b,2));
END;
/
Dimension 1 has:3
Dimension 2 has:4
```
