```ebnf+diagram
array_replace::= ARRAY_REPLACE "(" array_var "," compare_member "," replace_member ")"
```

ARRAY_REPLACE函数将数组（[数组变量](../../PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../PL参考手册/PL对象/自定义类型)，[数组类型](../数据类型（yashan模式）/用户自定义类型)）array_var中所有与compare_member相同的成员替换为replace_member，并返回替换后的数组。

本函数遵循如下规则：

* 当array_var的成员类型或compare_member的类型为非标量类型时报错。
* 本函数在PL中可以作为数组类型变量的初始化函数。
* 函数结果不能作为INSERT语句的value使用。
* 数组成员的替换不影响原有的数组变量，仅影响本函数的返回值。
* 本函数不支持向量化计算。

**array_var**

数组变量，其值可以为：

- 一个存在的数组，数组成员必须为[普通标量数据类型](../数据类型（yashan模式）/00数据类型（yashan模式）)。
- NULL，此时函数返回NULL。

**compare_member**

[通用表达式](../通用SQL语法/expr)，其值类型必须为与array_var数组成员可进行比较的类型，具体参考YashanDB的[比较运算规则](../运算符/比较运算符)。

**replace_member**

[通用表达式](../通用SQL语法/expr)，其类型必须为与array_var数组成员相同，或可进行隐式转换的数据类型。

示例（HEAP表）

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  arr arr_type := arr_type('a', 'b', 'cc', 'ddd', 'e');
  --使用ARRAY_REPLACE函数对数组初始化
  res arr_type := ARRAY_REPLACE(arr, 'cc', NULL);
BEGIN
  FOR i IN 1 .. res.COUNT LOOP
    IF res(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i ||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i ||': '|| res(i));
    END IF;
  END LOOP;
END;
/
1: a
2: b
3 is NULL
4: ddd
5: e
```
