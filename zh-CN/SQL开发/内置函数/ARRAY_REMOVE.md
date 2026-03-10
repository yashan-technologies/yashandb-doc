```ebnf+diagram
array_remove::= ARRAY_REMOVE "(" array_var "," compare_member ")"
```

ARRAY_REMOVE函数删除数组（[数组变量](../../全部手册/开发手册/PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../全部手册/开发手册/PL参考手册/PL对象/自定义类型)，[数组类型](../../全部手册/开发手册/SQL参考手册/数据类型（yashan模式）/用户自定义类型)）array_var中所有与compare_member相同的成员，并返回删除后的数组。

本函数遵循如下规则：

* 在PL中可以作为数组类型变量的初始化函数。
* 函数结果不能作为INSERT语句的value使用。
* array_var的成员类型或compare_member的类型为非标量类型时报错。
* 数组成员的删除不影响原有的数组变量，仅影响本函数的返回值。
* 本函数不支持向量化计算。

**array_var**

数组变量，其值可以为：

- 一个存在的数组，数组成员必须为[普通标量数据类型](../../全部手册/开发手册/SQL参考手册/数据类型（yashan模式）/00数据类型（yashan模式）)。
- NULL，此时函数返回NULL。

**compare_member**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值类型必须为与array_var数组成员可进行比较的类型，具体参考YashanDB的[比较运算规则](../../全部手册/开发手册/SQL参考手册/运算符/比较运算符)。

示例（HEAP表）

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  arr  arr_type := arr_type('123', '234', '333', '234', '23');
  --使用ARRAY_REMOVE函数对数组初始化
  res  arr_type := ARRAY_REMOVE(arr, 234);
BEGIN
  FOR i IN 1 .. arr.COUNT LOOP
    DBMS_OUTPUT.PUT_LINE('arr'||i||': '||arr(i));
  END LOOP;
  DBMS_OUTPUT.PUT_LINE('---------------');
  FOR i IN 1 .. res.COUNT LOOP
    DBMS_OUTPUT.PUT_LINE('res'||i||': '||res(i));
  END LOOP;
END;
/
arr1: 123
arr2: 234
arr3: 333
arr4: 234
arr5: 23
---------------
res1: 123
res2: 333
res3: 23
```
