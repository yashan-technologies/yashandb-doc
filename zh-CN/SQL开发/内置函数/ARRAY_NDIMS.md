```ebnf+diagram
array_ndims::= ARRAY_NDIMS "(" array_var ")"
```

ARRAY_NDIMS函数计算数组（[数组变量](../../全部手册/开发手册/PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../全部手册/开发手册/PL参考手册/PL对象/自定义类型)，[数组类型](../../全部手册/开发手册/SQL参考手册/数据类型（yashan模式）/用户自定义类型)）array_var的维度数（嵌套深度），返回一个INT类型的数值。

本函数不支持向量化计算。

**array_var**

一个已初始化的数组变量，当array_var为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SET serveroutput ON

CREATE OR REPLACE TYPE arr_udt_1 IS VARRAY(5) OF INT;
/

CREATE OR REPLACE TYPE arr_udt_2 IS VARRAY(5) OF arr_udt_1;
/

DECLARE
  a arr_udt_1 := arr_udt_1(2);
  b arr_udt_2 := arr_udt_2(a);
  c INT;
BEGIN
  DBMS_OUTPUT.PUT_LINE('Dimension of b: '||ARRAY_NDIMS(b));
  DBMS_OUTPUT.PUT_LINE('Dimension of b(1): '||ARRAY_NDIMS(b(1)));
END;
/
Dimension of b: 2
Dimension of b(1): 1
```
