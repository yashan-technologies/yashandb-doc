```ebnf+diagram
array_position::= ARRAY_POSITION "(" array_var "," compare_member [ "," start_locate ] ")"
```

ARRAY_POSITION函数以start_locate为起点查找数组（[数组变量](../../全部手册/开发手册/PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../全部手册/开发手册/PL参考手册/PL对象/自定义类型)，[数组类型](../../全部手册/开发手册/SQL参考手册/数据类型/用户自定义类型)）array_var的成员中第一个出现compare_member的位置，并返回INT类型的该位置值。

本函数不支持向量化计算。

**array_var**

数组变量，其值可以为：

- 一个存在的数组，数组成员必须为[普通标量数据类型](../../全部手册/开发手册/SQL参考手册/数据类型/00数据类型)。
- NULL，此时函数返回NULL。

**compare_member**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值类型必须为与array_var数组成员可进行比较的类型，具体参考YashanDB的[比较运算规则](../../全部手册/开发手册/SQL参考手册/运算符/比较运算符)。

**start_locate**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，不可为NULL，其值类型须为INT，或可隐式转换为INT。

当start_locate为负数，或者超过数组长度时，函数返回NULL。

示例（HEAP表）

```sql
CREATE OR REPLACE TYPE arr_position_type IS VARRAY(10) OF CHAR(5);
/

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd'), 'c') pos FROM DUAL;
         POS 
------------ 
           3

SELECT ARRAY_POSITION(arr_position_type('a', 'b', NULL, 'd', 'c'), NULL) pos FROM DUAL;
         POS 
------------ 
           3

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd', 'c'), 'c', 4) pos FROM DUAL;
         POS 
------------ 
           5

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd', 'c'), 1, 4) pos FROM DUAL;
         POS 
------------ 
           
```
