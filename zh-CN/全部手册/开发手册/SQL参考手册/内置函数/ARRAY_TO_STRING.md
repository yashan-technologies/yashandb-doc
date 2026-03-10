```ebnf+diagram
array_to_string::= ARRAY_TO_STRING "(" array_var "," split_string [ "," replace_string ] ")"
```

ARRAY_TO_STRING函数将数组（[数组变量](../../PL参考手册/PL语言基础/变量/集合变量)，[数组对象](../../PL参考手册/PL对象/自定义类型)，[数组类型](../数据类型/用户自定义类型)）array_var的成员以split_string作为分隔符进行连接，若数组成员中存在NULL且replace_string非空，则将NULL成员替换为replace_string后进行连接，函数返回一个VARCHAR类型的字符串。

本函数不支持向量化计算。

**array_var**

一个已初始化的数组变量，不可为NULL。

array_var的数组成员（当array_var为多层嵌套数组时，为最底层的数组成员）须为字符型或可隐式转换为字符型的其他类型。

**split_string**

[通用表达式](../通用SQL语法/expr)，须为字符型或可隐式转换为字符型的其他类型。split_string为NULL表示无分隔符连接。

**replace_string**

[通用表达式](../通用SQL语法/expr)，须为字符型或可隐式转换为字符型的其他类型。

示例（HEAP表）

```sql
CREATE OR REPLACE TYPE a2s_type IS VARRAY(20) OF VARCHAR(20);
/

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb','ccc'), ' ') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa bbb ccc  

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,ccc  

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',', 'NULL') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,NULL,ccc    

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), NULL) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaabbbccc                                                       

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',', NULL) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,ccc  
```
