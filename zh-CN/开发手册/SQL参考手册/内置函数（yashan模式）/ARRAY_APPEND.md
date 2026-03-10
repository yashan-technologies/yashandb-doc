```ebnf+diagram
array_append::= ARRAY_APPEND "(" array_var "," new_member ")"
```

ARRAY_APPEND函数将new_member添加到数组（[数组变量](../../PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../PL参考手册/PL对象/自定义类型)，[数组类型](../数据类型（yashan模式）/用户自定义类型)）array_var的末尾，并返回添加后的数组结果。

本函数遵循如下规则：

* 在PL中可以当作特殊的数组初始化函数使用。
* 函数结果不能作为INSERT语句的value使用。
* 不能对包含非标量类型成员的数组使用本函数。
* 执行本函数后，array_var中的成员数量并不会发生变化。
* 本函数不支持向量化计算。

**array_var**

数组变量，其值可以为：

- 一个存在的数组，数组成员必须为[普通标量数据类型](../数据类型（yashan模式）/00数据类型（yashan模式）)。
- NULL，此时将会生成一个以new_member的类型作为成员类型的数组（new_member不能为NULL），且新数组仅包含new_member一个成员。

**new_member**

[通用表达式](../通用SQL语法/expr)，其类型必须为与array_var数组成员相同，或可进行隐式转换的数据类型。

当array_var不为NULL时，new_member可以为NULL，表示向array_var数组末尾添加一个NULL成员。

示例（HEAP表）

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  a arr_type := arr_type('a', 'b', 'c');
  b arr_type;
BEGIN
  -- 向a的末尾添加一个成员'd'，生成临时数组并将该数组赋值给b
  b := ARRAY_APPEND(a, 'd');
  FOR i IN 1 .. b.COUNT LOOP
    IF b(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(b(i));
    END IF;
  END LOOP;
END;
/
a
b
c
d

-- 首个参数为NULL时生成新数组
DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  b arr_type;
BEGIN
  -- 生成新数组  
  b := ARRAY_APPEND(NULL, 'abcd');
  FOR i IN 1 .. b.COUNT LOOP
    IF b(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i||' is: '||b(i));
    END IF;
  END LOOP;
END;
/
1 is: abcd
```
