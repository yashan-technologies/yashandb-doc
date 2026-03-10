```ebnf+diagram
initcap::= INITCAP "(" expr ")"
```

INITCAP函数将[expr](../通用SQL语法/expr)表示的字符串进行单词分隔，并将分隔后的单词转换为首字母大写，非首字母小写样式。

函数将expr中的除英文字母、数字字符外的其他字符认定为分隔符并据此分隔单词，包括标点符号、普通符号、控制字符、希腊字母以及中文字符等。

**expr**

expr的值须为字符型或可转换为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为NCLOB/NCHAR/NVARCHAR时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT INITCAP('1wWork who i——s i1w') res FROM DUAL;
RES
---------------------
1wwork Who I——S I1w 

SELECT INITCAP(SYSDATE||':today') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2022-11-08 02:41:17:Today   

-- 对浮点型数据先转为字符型
SELECT INITCAP(CAST('inf' AS FLOAT)||'/'||CAST('1.11' AS FLOAT)) res FROM DUAL;
RES                   
--------------------- 
Inf/1.11000001e+000    

-- 对二进制数据先转为字符型
SELECT INITCAP(b'010101') res FROM DUAL;
RES 
------------------ 
21    
```
