```ebnf+diagram
treat::= TREAT "(" expr AS [schema "."] type ")" 
```

TREAT函数用于修改[expr](../通用SQL语法/expr)定义的类型。

执行本函数的数据库用户必须拥有对应type的EXECUTE权限。

- expr和type的类型必须是[用户自定义类型](../数据类型/用户自定义类型)的OBJECT类型。
- 如果expr和type是同一类型，函数返回值为expr。  
- type必须是expr定义类型的超类型或子类型，否则TREAT编译错误。
- 如果expr的值不是type类型或type的子类型，TREAT执行错误，在SQL中返回NULL，在PL中产生一个异常。

示例

```sql
CREATE OR REPLACE TYPE udt_object_sp FORCE IS OBJECT (
    branch_no CHAR(4),
    branch_name VARCHAR2(200)
) NOT FINAL;
/

CREATE OR REPLACE TYPE udt_object_sub FORCE UNDER udt_object_sp (
    area_no CHAR(2),
    address VARCHAR2(200)
) NOT FINAL;
/

DECLARE
    obj1 udt_object_sub := udt_object_sub('0101','Beijing','01','North Street');
    obj2 udt_object_sp;
BEGIN
    obj2 := TREAT (obj1 AS udt_object_sp);
    DBMS_OUTPUT.PUT_LINE(obj2.branch_name);
END;
/

DECLARE
    obj1 udt_object_sp := udt_object_sub('0101','Beijing','01','North Street');
    obj2 udt_object_sub;
BEGIN
    obj2 := TREAT (obj1 AS udt_object_sub);
    DBMS_OUTPUT.PUT_LINE(obj2.branch_name);
END;
/
```
