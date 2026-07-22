```ebnf
nvl = NVL "(" expr1 "," expr2 ")".
```

NVL函数从左向右判断，返回两个[expr](../通用SQL语法/expr)参数值中首个非空的表达式的值。

若expr1和expr2都为NULL，则函数返回NULL。

expr1和expr2均支持LOB类型隐式转换。

当expr1和expr2的数据类型不相同时，函数先执行类型转换，若两个数据类型之间无法按照一定的规则进行转换则返回类型转换错误。类型转换规则如下：

*   若expr1为字符型，则将expr2转换为VARCHAR类型，且返回值为VARCHAR类型。
*   若expr1与expr2都为数值型，函数将会确定所有数据中具有最高精度的数据类型，将所有expr的执行结果转为该类型，且返回值也为该类型。
*   若expr1和expr2都为DATE、TIMESTAMP、时区类型时，返回类型优先级为TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE。
*   若expr1为未知类型，将expr1转换为expr2的类型。
*   若expr2为未知类型，将expr2转为expr1的类型。
*   若expr1与expr2皆为未知类型，将expr1与2都转为VARCHAR类型。
*   若expr1与expr2类型已知且类型不相同，同时不满足上述的情况1与情况2时，会强制将expr2转为expr1的数据类型，如果不满足转换条件则返回invalid datatype错误，具体类型转换规则同[CAST](./CAST)函数描述。

示例

```sql
SELECT NVL(now(),'2011-05-11 12:28:04') res FROM DUAL;
RES                               
--------------------------------
2021-12-05 18:02:48                     
 
SELECT NVL('a',1) res FROM DUAL;
RES    
-----
a
```
