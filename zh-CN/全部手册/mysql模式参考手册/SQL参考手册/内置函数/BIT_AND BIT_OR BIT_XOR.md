```ebnf+diagram
bit_and::= BIT_AND "(" [ALL] expr ")" 
bit_or::= BIT_OR "(" [ALL] expr ")" 
bit_xor::= BIT_XOR "(" [ALL] expr ")" 
```

BIT_AND/BIT_OR/BIT_XOR聚集函数对[expr](../通用SQL语法/expr)进行位计算，并返回一个64位BIGINT UNSIGNED的数值。

**expr**

通用表达式，其值须为数值型或可隐式转化为数值型的其他类型。

- 位计算时，默认初始值设置为所有位为1的64位BIGINT UNSIGNED数据，输入的expr为NULL时则跳过，选择下一个expr进行位计算。

- 当expr为数值型小数时，将进行四舍五入后进行位计算；字符类型小数则直接去掉小数点后的尾数。

- 不允许expr数据类型为BINARY和BLOB。

示例（单机HEAP表）

```sql
CREATE TABLE bit_tab(c1 INT);
INSERT INTO bit_tab VALUES(1);
INSERT INTO bit_tab VALUES(0);

SELECT BIT_AND(c1) FROM bit_tab ;
return 0;

SELECT bit_or(c1) FROM bit_tab ;
return 1;

SELECT bit_xor(c1) FROM bit_tab ;
return 1;

SELECT BIT_AND(null) FROM dual;
        bit_and(null) 
--------------------- 
 18446744073709551615

SELECT bit_or(null) FROM dual;
         bit_or(null) 
--------------------- 
                    0

SELECT bit_xor(null) FROM dual;
        bit_xor(null) 
--------------------- 
                    0
```
