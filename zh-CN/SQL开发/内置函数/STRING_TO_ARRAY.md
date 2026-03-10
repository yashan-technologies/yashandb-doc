```ebnf+diagram
string_to_array::= STRING_TO_ARRAY "(" src_string "," split_string [ "," replace_string ] ")"
```

STRING_TO_ARRAY函数将字符串src_string以split_string作为分隔符进行切分生成一个数组，当被切分的成员与replace_string相同时，则将被切分成员替换为NULL后再生成数组（[数组变量](../../全部手册/开发手册/PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../全部手册/开发手册/PL参考手册/PL对象/自定义类型)，[数组类型](../../全部手册/开发手册/SQL参考手册/数据类型（yashan模式）/用户自定义类型)）并返回。

本函数遵循如下规则：

* 本函数不支持向量化计算。
* 最终返回的数组为隐式定义的数组变量，其成员数量上限最大为INT类型上限（2147483647），其成员数据类型为VARCHAR(n)，n的值为生成数组中的最长成员的长度。
* 本函数可以在PL中作为数组的初始化函数进行使用，但存在赋值对象限制，被赋值对象需要能承载本函数生成的数组，即其成员上限需大于等于本函数返回数组的成员上限，成员长度需大于等于本函数返回数组的长度。

**src_string**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，须为字符型或可隐式转换为字符型的其他类型（LOB类型支持隐式转换）。当src_string为NULL时，函数返回NULL。

**split_string**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，须为字符型或可隐式转换为字符型的其他类型（LOB类型支持隐式转换）。当split_string为NULL时，函数将src_string按每个单字符逐一切分。

**replace_string**

[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，须为字符型或可隐式转换为字符型的其他类型（LOB类型支持隐式转换）。

示例

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(10);
  a arr_type;
BEGIN
  a := STRING_TO_ARRAY('a,b,c,d,e', ',');
  FOR i IN 1 .. a.COUNT LOOP
    DBMS_OUTPUT.PUT_LINE(i||' is: '||a(i));
  END LOOP;
END;
/
1 is: a
2 is: b
3 is: c
4 is: d
5 is: e

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  --使用STRING_TO_ARRAY函数对数组初始化
  a arr_type := STRING_TO_ARRAY(',a,b,c,', ',','b');
BEGIN
  FOR i IN 1 .. a.COUNT LOOP
    IF a(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i||' is: '||a(i));
    END IF;
  END LOOP;
END;
/
1 is NULL
2 is: a
3 is NULL
4 is: c
5 is NULL
```
